# friboo

A utility library to write microservices in Clojure. The library provides some components that can be used with the
[component lifecycle framework](https://github.com/stuartsierra/component).

## Usage

Dependency:

    [org.zalando.stups/friboo <latest version>]

from Maven central.

Most simple case:

```clojure
(ns examples
  (:require [org.zalando.stups.friboo.system :as system]
            [com.stuartsierra.component :as component])
  (:gen-class))

(defn -main [&args]
  (let [configuration (system/load-configuration)
        system (component/new-system
          ; your system setup, see https://github.com/stuartsierra/component
          )]
    (system/run configuration)))
```

### Configuration options

* `:system-log-level` can be used to set the root logger to something different than `INFO`.

## Helpful components

Friboo mainly provides some helpful utilities which are mostly presented as components.

### HTTP component

The HTTP component is generated on demand by the `def-http-component` macro. You can define which dependencies your
API functions require.

```clojure
(ns myapi
  (:require [org.zalando.stups.friboo.system.http :refer [def-http-component]))

(def-http-component MyAPI "my-api.yaml" [db scheduler])

(defn my-api-function [parameters request db scheduler]
  ; your implementation that can use 'db' as well as 'scheduler' dependencies
  )
```

The first argument of your function will always be a flattened map (parameter name -> parameter value) without `in`
categorisation. This does violate the swagger spec which calls parameter names only unique in combination with your
`in` type. You have to take care during modelling your API.

#### Configuration options

The component has to be initialized with its configuration in the `:configuration` key.

    (map->MyAPI {:configuration {:port        8080
                                 :cors-origin "*.zalando.de"}})

* `:cors-origin` may be set to a domain mask for CORS access (e.g. `*.zalando.de`).
* All configurations that Jetty supports.

### DB component

The DB component is generated on demand by the `def-db-component` macro. The DB component is itself a `db-spec`
compliant data structure, backed by a connection pool.

```clojure
(ns mydb
  (:require [org.zalando.stups.friboo.system.db :refer [def-db-component]))

(def-db-component MyDB)
```

#### Configuration options

The component has to be initialized with its configuration in the `:configuration` key.

    (map->MyDB {:configuration {:subprotocol "postgresql"
                                :subname     "localhost/mydb"}})

TODO link to jdbc documentation, pool specific configuration like min- and max-pool-size

## Real-world usage

TODO link to actual implementations like Kio

TODO HINT: set java.util.logging.manager= org.apache.logging.log4j.jul.LogManager to have proper JUL logging

## License

Copyright © 2015 Zalando SE

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
