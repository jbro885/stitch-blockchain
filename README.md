# stitch-blockchain
Demo for MongoDB stitch as a store for blockchains

# Introduction

Revolutionary Blockchain technology meets MongoDB’s empowering backend service and Database for modern applications to create next generation solutions.
Using Stitch platform will allow us to design and build the next decentralize, trusted and fraud free applications utilizing its newest features.

# Overview

* In this workshop you will get to see live coding and build a hands-on application that is build with Stitch implementing a decentralized blockchain application. You will be able to use newest Stitch features such as Advanced Javascript functions and event driven notifications (Change Streams).

* The nodes will use the anonymous Stitch authentication consuming only public information. Each node is allowed to edit only its information while the node consensus will be enforced by Stitch functions. Notifications will be the main component that notifies subscribers and pushes the processes forward until completion.

* End peers (example wallets) may use a variety of authentication mechanisms available by Stitch to keep their data private and encrypted.

# Configuring and running
1. Setup an Atlas cluster and stitch. See the following [documentation](https://docs.mongodb.com/stitch/getting-started/).
2. [Import](https://docs.mongodb.com/stitch/import-export/create-stitch-app/) the stitch skelaton app from this repo: stitch-blockchain.zip.
3. Create the following view in your cluster under database `transactions`:
```
db.createView("blockchain","pending_blocks",
			[	{
					"$match" : {
						"index" : 0,
						"approvedByMajority" : true
					}
				},
				{
					"$graphLookup" : {
						"from" : "pending_blocks",
						"startWith" : "$previousHash",
						"connectFromField" : "hash",
						"connectToField" : "previousHash",
						"as" : "chain"
					}
				},
				{
					"$unwind" : "$chain"
				},
				{
					"$sort" : {
						"chain.index" : 1
					}
				},
				{
					"$group" : {
						"_id" : "$_id",
						"chain" : {
							"$push" : "$chain"
						}
					}
				},
				{
					"$project" : {
						"chain" : 1,
						"_id" : 0
					}
				}
			]);
```
4. Create a mongodb readOnly user on all databases. Verify that the cluster name in the imported application is associated with your cluster name.
5. Edit the `main.js` file with the MongoDB credentials created in step 1 and the correct connection string for your Atlas cluster. Run the production of the blocks with:
```
node main.js
```
