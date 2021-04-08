# ES toolkit

These are a collection of scripts I whipped up to originally assist in demoing some upcoming EQL functionality for kibana's detections. It's since been expanded to include various data-generation scripts.

### Requirements

I'm on OSX so most of this assumes BSD utilities; in addition, the scripts require:

- [`jq`](https://formulae.brew.sh/formula/jq) for JSON manipulation
- [`curl`](https://formulae.brew.sh/formula/curl) to perform network requests
- [`gdate`](https://formulae.brew.sh/formula/coreutils) for generation of zulu timestamps in MS

### Configuration

The proceeding scripts, where relevant, respect the following environment variables:

- `ELASTICSEARCH_USERNAME`
  - name used when authenticating to elasticsearch
  - defaults to `elastic`
- `ELASTICSEARCH_PASSWORD`
  - password used when authenticating to elasticsearch
  - defaults to `changeme`
- `ELASTICSEARCH_URL`
  - URL used when authenticating to elasticsearch
  - defaults to `http://localhost:9200`
- `ELASTICSEARCH_INDEX`
  - name of the index being targeted
  - defaults to `logs-eql_auth_demo`

## Scripts Overview

Note: all scripts are meant to be invoked from the root of the project.

### bulk_create_events

Creates N events each with a unique, current @timestamp field and a host.name as specified.

```bash
# create 10 events with a host.name of "host.name"
./bulk_create_events

# create 100 events with a host.name of "host.name"
./bulk_create_events 100

# create 20 events with a host.name of "laptop.local"
./bulk_create_events 100 laptop.local
```

### create_random_docs

Creates N events each with a unique, current @timestamp field and a random field name and value. Used to create a mapping explosion on an index with dynamic mappings

```bash
# create 10 events a random field/value: { [random()]: random() }
./create_random_docs

# create 100 events a random field/value
./create_random_docs 100
```

### create_suspicious_login

Simulates a suspicious login by sequentially generating n failed login events proceeded by 1 successful login event. Timestamps of the created events use the current system time with millisecond precision.

```bash
# create 3 failures + 1 success for user.name "default_username"
./create_suspicious_login

# create 3 failures + 1 success for user.name "my_name"
./create_suspicious_login my_name

# create 10 failures + 1 success for user.name "my_name"
./create_suspicious_login my_name 10
```

### create_failed_login

Simulates a failed login attempt by sequentially generating n failed login events. Timestamps of the created events use the current system time with millisecond precision.

```bash
# create 3 failures for user.name "default_username"
./create_failed_login

# create 3 failures for user.name "my_name"
./create_failed_login my_name

# create 10 failures for user.name "my_name"
./create_failed_login my_name 10
```

### create_successful_login

Simulates a successful login attempt by sequentially generating n successful login events. Timestamps of the created events use the current system time with millisecond precision.

```bash
# create 1 login for user.name "default_username"
./create_successful_login

# create 1 login for user.name "my_name"
./create_successful_login my_name

# create 10 logins for user.name "my_name"
./create_successful_login my_name 10
```

### rebuild_index

First attempts to delete, then rebuild the index specified by `$ELASTICSEARCH_INDEX` with the mappings in ./data/mappings.json. Leverages the utility scripts `delete_index` and `create_index` under the hood.
