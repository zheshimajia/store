# store

store is a distributed reliable key-value store for the most critical data of a distributed system. This library is written in Go and uses the Raft consensus algorithm to manage a highly-available replicated log.

## Getting started


```
package main

import (
	"fmt"

	"github.com/lodastack/store/cluster"
)

func main() {
	opts := cluster.Options{
		// store bind TCP listen
		Bind: "127.0.0.1:9000",
		// store data dir
		DataDir: "/tmp/store",
	}

	cs, err := cluster.NewService(opts)
	if err != nil {
		fmt.Printf("new store service failed: %s", err)
		return
	}

	if err := cs.Open(); err != nil {
		fmt.Printf("failed to open cluster service failed: %s", err)
		return
	}

	// If join was specified, make the join request.
	nodes, err := cs.Nodes()
	if err != nil {
		fmt.Printf("get nodes failed: %s", err)
		return
	}
	
	fmt.Println(nodes)
}

```

## API

```
	// Join joins the node, reachable at addr, to the cluster.
	Join(addr string) error

	// Remove removes a node from the store, specified by addr.
	Remove(addr string) error

	// Create a bucket, via distributed consensus.
	CreateBucket(name []byte) error

	// Create a bucket via distributed consensus if not exist.
	CreateBucketIfNotExist(name []byte) error

	// Remove a bucket, via distributed consensus.
	RemoveBucket(name []byte) error

	// Get returns the value for the given key.
	View(bucket, key []byte) ([]byte, error)

	// ViewPrefix returns the value for the keys has the keyPrefix.
	ViewPrefix(bucket, keyPrefix []byte) (map[string][]byte, error)

	// Set sets the value for the given key, via distributed consensus.
	Update(bucket []byte, key []byte, value []byte) error

	// RemoveKey removes the key from the bucket.
	RemoveKey(bucket, key []byte) error

	// Batch update values for given keys in given buckets, via distributed consensus.
	Batch(rows []model.Row) error

	// GetSession returns the sression value for the given key.
	GetSession(key interface{}) interface{}

	// SetSession sets the value for the given key, via distributed consensus.
	SetSession(key, value interface{}) error

	// DelSession delete the value for the given key, via distributed consensus.
	DelSession(key interface{}) error

	// Backup database.
	Backup() ([]byte, error)

	// Restore restores backup data file.
	Restore(backupfile string) error

	// Peers return the map of Raft addresses to API addresses.
	Peers() (map[string]string, error)

	// Nodes returns the list of current peers.
	Nodes() ([]string, error)

	// WaitForLeader blocks until a leader is detected, or the timeout expires.
	WaitForLeader(timeout time.Duration) (string, error)

	// Close closes the store. If wait is true, waits for a graceful shutdown.
	Close(wait bool) error

```