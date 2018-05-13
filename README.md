Consul leader election [![Build Status](https://travis-ci.org/dmitriyGarden/consul-leader-election.svg?branch=master)](https://travis-ci.org/dmitriyGarden/consul-leader-election)
======================

This package provides leader election through consul

 https://www.consul.io/docs/guides/leader-election.html

 How to use
 ==========
 ```go
 package main

    import(
        "github.com/hashicorp/consul/api"
        "github.com/dmitriyGarden/consul-leader-election"
    )
    func main(){
        conf := api.DefaultConfig()
    	consul, _ := api.NewClient(conf)
    	e := election.NewElection(consul, []string{"healthID"}, "my.servicename")
    	e.logLevel = election.LogDebug
    	// start election
    	var wg sync.WaitGroup
    	wg.Add(1)
    	go func(){
    	        defer wg.Done()
    	        e.Init()
    	}()
    	time.Sleep(30 * time.Second)
    	if e.IsLeader() {
    		fmt.Println("I'm a leader!")
    	}
    	// re-election
    	e.ReElection()
    	time.Sleep(30 * time.Second)
    	if e.IsLeader() {
    		fmt.Println("I'm a leader!")
    	}
    	e.Stop()
        wg.Wait()
    }
