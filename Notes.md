****************
Each node calls the SystemAPIClient Info interface to get the CPU and Memory usage.
Similarly there is already an interface for Diskusage


swarmd/main.go:
main()
client, err := engineapi.NewClient(engineAddr, "", nil, nil)
executor := dockerapi.NewExecutor(client)
n, err := node.New(&node.Config{
				Hostname:         hostname,
				ForceNewCluster:  forceNewCluster,
				ListenControlAPI: unix,
				ListenRemoteAPI:  addr,
				JoinAddr:         managerAddr,
				StateDir:         stateDir,
				JoinToken:        joinToken,
				ExternalCAs:      externalCAOpt.Value(),
				Executor:         executor,
				HeartbeatTick:    hb,
				ElectionTick:     election,
				AutoLockManagers: autolockManagers,
				UnlockKey:        unlockKey,
			})
			
n.Start(ctx)
n.Ready()

node/node.go:
 n.run(ctx)
         agentErr = n.runAgent(ctx, db, securityConfig, agentReady)
             a, err := agent.New(&agent.Config{.. here ConnBroker:       n.connBroker, specifies connection brker created when creating node.
             a.Start(ctx)
             
agent/agent.go:
a.Start(ctx)
        a.run(ctx)
                nodeDescription, err := a.nodeDescriptionWithHostname(ctx, nodeTLSInfo)  //nodeDescriptionWithHostname -->a.config.Executor.Describe(ctx) which gets info
                session = newSession(ctx, a, delay, session.sessionID, nodeDescription)  // new session created when the node gets updated.
                    In newSession we also::
                        cc, err := agent.config.ConnBroker.Select(     //this returns the connections
                        i.e. // Select a manager from the set of available managers, and return a connection. in case of remote it returns tCP connection.
                            func (b *Broker) Select(dialOpts ...grpc.DialOption) (*Conn, error) {




