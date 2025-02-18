# About Balanced VRP Clustering

This gem aims to help you solving your Vehicle Routing Problems by generating one sub-problem per vehicle. It is an adaptation of kmeans algorithm (based ok ai4r library : https://github.com/SergioFierens/ai4r), including notions of balance.

# Gem generation (for contributors)

Within gem project use
```gem build balanced_vrp_clustering.gemspec``` and ```gem install balanced_vrp_clustering``` to generate gem.

  # Usage

##### Include gem in your own ruby project

In your Gemfile :

```gem 'balanced_vrp_clustering'```

In your ruby script :

```require 'balanced_vrp_clustering'```

##### Clustering example

Initialize your clusterer tool c and provided needed data :

```c = Ai4r::Clusterers::BalancedVRPClustering.new```

```
vehicles = {
  "id": vehicle_id e.g. 'vehicle_1',
  "depot": indice of corresponding depot in matrix, if any provided. Otherwise, [latitude, longitude] of vehicle depot,
  "capacities": { "unit_1": 10, "unit_2": 100 },
  "skills": list of skills e.g. ['big', 'heavy'],
  "day_skills": list of available days -- e.g. ['monday', 'tuesday'],
  "duration": Total work duration. If duration is 0 for all vehicles, duration_to_from_depot calculation and cut_limit update are disabled.
  "total_work_days": Number of days the vehicle works (default: 1)
}
c.vehicles = vehicles
c.max_iterations = max_iterations
c.distance_matrix = distance_matrix # provide distance matrix if any, otherwise flying distance will be used
```

Run clustering :

```
data_items = items.collect{ |i|
  i[:latitude],
  i[:longitude],
  i[:id],
  { "unit_1": i[:quantities]['unit_1'], "unit_2": i[:quantities]['unit_2'] },
  { "v_id": id of vehicle that should be assigned to this item if any,
    "skills": list of skills,
    "day_skills": list of available days -- e.g. ['monday', 'tuesday'],
    "matrix_index": only if any matrix was provided
  }
}
cut_symbol = :duration # or any other unit (:unit_1, :unit_2) to be used for balancing clusters
related_item_indices = { shipment: [[0, 1] [2, 3]], same_route: [[4, 5]]} # Available relations are as follows
                                                                          # LINKING_RELATIONS = %i[order same_route sequence shipment]
                                                                          # BINDING_RELATIONS = %i[order same_route sequence]
ratio = 1 # by default, used to over/underestimate vehicles limits
c.logger = Logger.new(STDOUT) # for debug output
c.build(DataSet.new(data_items: data_items), cut_symbol, related_item_indices, ratio, options)```

cut_symbol is the referent unit to use when balancing clusters. This unit should exist in both vehicles and data_items structures.
```

Get clusters back :

```
puts c.clusters.size # same same as vehicles
clusters = c.clusters.collect{ |generated_cluster|
  generated_clusters.data_items.collect{ |item| item[2] } # item id
}
```

Items have same structure as data_items initially provided.

# Test

```
APP_ENV=test bundle exec rake test
```
