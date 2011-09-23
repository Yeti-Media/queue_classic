# queue_classic
v1.0.0

queue_classic is a PostgreSQL-backed queueing library that is focused on
concurrent job locking, minimizing database load & providing a simple &
intuitive user experience.

queue_classic features:

* Support for multiple queues with heterogeneous workers
* Utilization of Postgres' PUB/SUB
* JSON encoding for jobs
* Forking workers
* Postgres' rock-solid locking mechanism
* Long term support

## Quick Start

See doc/installation.md for Rails instructions

```bash
  $ createdb queue_classic_test
  $ psql queue_classic_test
  psql=# CREATE TABLE queue_classic_jobs (id serial, details text, locked_at timestamp);
  psql=# CREATE INDEX queue_classic_jobs_id_idx ON queue_classic_jobs (id);
  $ export QC_DATABASE_URL="postgres://username:password@localhost/queue_classic_test"
  $ gem install queue_classic
  $ ruby -r queue_classic -e "QC::Database.new.load_functions"
  $ ruby -r queue_classic -e "QC.enqueue("Kernel.puts", "hello world")"
  $ ruby -r queue_classic -e "QC::Worker.new.start"
```

## Configure

```bash

# Enable logging.
$VERBOSE

# Specifies the database that queue_classic will rely upon.
$QC_DATABASE_URL || $DATABASE_URL

# For strict FIFO set to 1. Otherwise, worker will 
# attempt to lock a job in this top region.
# Default: 9
$QC_TOP_BOUND

# If you want your worker to fork a new 
# child process for each job, set this var to 'true'
# Default: false
$QC_FORK_WORKER

# The worker uses an exp backoff algorithm
# if you want high throughput don't use Kernel.sleep
# use LISTEN/NOTIFY sleep. When set to true, the worker's 
# sleep will be preempted by insertion into the queue.
# Default: false
$QC_LISTENING_WORKER

# The worker uses an exp backoff algorithm. The base of
# the exponent is 2. This var determines the max power of the
# exp.
# Default: 5 which implies max sleep time of 2^(5-1) => 16 seconds
$QC_MAX_LOCK_ATTEMPTS

# This var is important for consumers of the queue.
# If you have configured many queues, this var will
# instruct the worker to bind to a particular queue.
# Default: queue_classic_jobs --which is the default queue table.
$QUEUE


```

## Hacking on queue_classic

### Dependencies

* Ruby 1.9.2
* Postgres ~> 9.0
* Rubygems: pg ~> 0.11.0

### Running Tests

```bash
  $ bundle
  $ createdb queue_classic_test
  $ export QC_DATABASE_URL="postgres://username:pass@localhost/queue_classic_test"
  $ rake
```

### Building Documentation

If you are adding new features, please document them in the doc directory. Also,
once you have the markdown in place, please run: ruby doc/build.rb to make HTML
for the docs.

## Other Resources

###[Documentation](https://github.com/ryandotsmith/queue_classic/tree/master/doc)

###[Example Rails App](https://github.com/ryandotsmith/queue_classic_example)

###[Discussion Group](http://groups.google.com/group/queue_classic "discussion group")
