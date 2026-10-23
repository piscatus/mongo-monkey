# Mongo Monkey Documentation

## Introduction

The Mongo Replica Set that makes up the Database Connection

## Prerequisites

Before running the Mongo Monkey application, please ensure the follow prerequisites are met:

1. **Docker**:

   - Docker engine installed and running on your system
   - https://docs.docker.com/engine/install/ubuntu/
   - Docker network has been created : mongo-net
   - docker network create mongo-net

2. **Hosts File**:

   - Update the Host Machines Hosts File:
   - sudo nano /etc/hosts
   - Add "127.0.0.1 mongo-monkey"
   - Add "127.0.0.1 mongo-marsupial"

3. **Mongo-Monkey Directory**

  - Contains a .env with port 27017

3. **Mongo-Marsupial Directory**

  - Contains a .env with port 27018

3. (Optional) **MongoDB Compass**:

    - MongoDB Compass installed and running on your system.
    - https://www.mongodb.com/docs/compass/current/install/

5. **MongoDB Shell**:

    - MongoDB Shell installed and running on your system.
    - https://www.mongodb.com/docs/mongodb-shell/install/

6. (OPTIONAL) **BACKUP**:

    - Backup all collections
    - `sudo mongodump \
  --uri "mongodb://mongo-monkey:27017,mongo-marsupial:27018,mongo-meerkat:27019/?replicaSet=rs0" \
  --db data \
  --out /opt/mongo-monkey/backupData/backupName`
    - Backup one collection
    - `mongodump --db data --collection leaderboards --out /opt/mongo-monkey/backupData/backupName`

7. (OPTIONAL) **RESTORE**:

    Restore all collections:
    `sudo mongorestore \
    --uri "mongodb://mongo-monkey:27017,mongo-marsupial:27018,mongo-meerkat:27019/?replicaSet=rs0" \
    --drop \
    --db data /opt/mongo-monkey/backupData/noData/data`

    Restore one collection:
    `mongorestore --db data --collection leaderboards /opt/mongo-monkey/backupData/data/leaderboards.bson`

8. **File and Former Permissions Updated**:

   - Due to docker-compose specifying user: "1000:1000" you may need to:
   - `sudo chown -R 1000:1000 mongo-monkey/`

## Instructions

To run the application, navigate to the root of the project directory and execute:
sudo docker compose up -d

(Optional) Port Forward 27017
- `ssh -L <port>:localhost:<port> <user>@<server-ip>`

Execute the following mongo shell commands:
- `mongosh --host mongo-monkey --port 27017`

- `rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "mongo-monkey:27017" },
    { _id: 1, host: "mongo-marsupial:27018" },
    { _id: 2, host: "mongo-meerkat:27019", arbiterOnly: true }
  ]
})`

- `rs.status();`

(Optional) Port Forward 27018
- `ssh -L <port>:localhost:<port> <user>@<server-ip>`

Execute the following mongo shell commands:
- `mongosh --host localhost --port 27018`
- `rs.status();`

The command above will mention the Replica Sets are Synchronized

To Connect Mongo DB Compass to the replicaset, in your terminal, all three port forwards must be active:
`ssh \
  -L 27017:mongo-monkey:27017 \
  -L 27018:mongo-marsupial:27018 \
  -L 27019:mongo-meerkat:27019 \
  anon@192.168.86.250`

Mongo DB Compass SSH Connection String:
`mongodb://mongo-monkey:27017,mongo-marsupial:27018,mongo-meerkat:27019/?readPreference=primaryPreferred&replicaSet=rs0`

To Connect mongosh to the replicaset, in your terminal:
mongosh "mongodb://mongo-monkey-dev:27014,mongo-marsupial-dev:27015,mongo-meerkat-dev:27016/?replicaSet=rs1&readPreference=primaryPreferred"

`use data`

Option 1 (Restore)

Restore form backupData/init/data

Option 2 (Manual)

Create Necessary Collections:
- `db.createCollection("activities")`
- `db.createCollection("aliases")`
- `db.createCollection("anglers")`
- `db.createCollection("commands")`
- `db.createCollection("creatures")`
- `db.createCollection("currencies")`
- `db.createCollection("drops")`
- `db.createCollection("emojis")`
- `db.createCollection("guildConfigurations")`
- `db.createCollection("guildWallets")`
- `db.createCollection("leaderboards")`
- `db.createCollection("messages")`
- `db.createCollection("pickups")`
- `db.createCollection("profanities")`
- `db.createCollection("queues")`
- `db.createCollection("transactions")`
- `db.createCollection("userDetails")`
- `db.createCollection("userItems")`
- `db.createCollection("userWallets")`

Create Necessary Indexes:
- `db.activities.createIndex({ guildId: 1, channelId: 1, userId: 1 },{ unique: true });`
- `db.aliases.createIndex({ guildId: 1, singular: 1 }, { unique: true });`
- `db.anglers.createIndex({ guildId: 1, userId: 1 }, { unique: true });`
- `db.creatures.createIndex({ name: 1 }, { unique: true });`
- `db.currencies.createIndex({ ticker: 1 }, { unique: true });`
- `db.drops.createIndex({ dropId: 1 }, { unique: true });`
- `db.emojis.createIndex({name: 1}, { unique: true });`
- `db.guildConfigurations.createIndex({ guildId: 1 }, { unique: true });`
- `db.guildWallets.createIndex({ guildId: 1 }, { unique: true });`
- `db.leaderboards.createIndex({ guildId: 1, userId: 1 }, { unique: true });`
- `db.pickups.createIndex({ dropId: 1, userId: 1 }, { unique: true });`
- `db.profanities.createIndex({ singular: 1, plural: 1 }, { unique: true });`
- `db.userDetails.createIndex({ userId: 1 }, { unique: true });`
- `db.userItems.createIndex({ userId: 1 }, { unique: true });`
- `db.userWallets.createIndex({ userId: 1 }, { unique: true });`
- `db.depositAddresses.createIndex({ ticker: 1, userId: 1 }, { name: "ticker_userId_unique", unique: true });`
- `db.depositAddresses.createIndex({ ticker: 1, address: 1 }, { name: "ticker_address_unique", unique: true });`
- `db.depositAddresses.createIndex({ ticker: 1, addressIndex: 1 }, { name: "ticker_addressIndex_unique", unique: true });`
- `db.depositRecords.createIndex({ ticker: 1, txid: 1, addressIndex: 1, outputIndex: 1 }, { name: "ticker_txid_index_output_unique", unique: true });`
- `db.transactions.createIndex({ timestamp: -1 }, { name: "timestamp_desc" });`
- `db.transactions.createIndex({ primaryUserId: 1, timestamp: -1 }, { name: "primaryUserId_timestamp" });`
- `db.transactions.createIndex({ secondaryUserId: 1, timestamp: -1 }, { name: "secondaryUserId_timestamp" });`
- `db.transactions.createIndex({ primaryReceiverIds: 1, timestamp: -1 }, { name: "primaryReceiverIds_timestamp" });`
- `db.transactions.createIndex({ secondaryReceiverIds: 1, timestamp: -1 }, { name: "secondaryReceiverIds_timestamp" });`
- `db.trivias.createIndex({ hash: 1 }, { name: "hash_unique", unique: true });`
- `db.trivias.createIndex({ enabled: 1, category: 1, lastUsed: 1 }, { name: "enabled_category_lastUsed" });`
- `db.trivias.createIndex({ source: 1, enabled: 1 }, { name: "source_enabled" });`

Notes:

- The `depositNotices` indexes (`ticker_txid_index_unique` and the 14-day `timestamp_ttl`) are created by the API itself at startup; nothing to run by hand.
- Databases that received the old `depositRecords` index `ticker_txid_index_unique` (ticker, txid, addressIndex) need a one-time migration: create `ticker_txid_index_output_unique` as above, then `db.depositRecords.dropIndex("ticker_txid_index_unique")`. The API expects the four-field rule since the combined Monero sends change.

Finally:

Import Necessary Data:
- `sudo mongoimport --uri "mongodb://mongo-monkey:27017,mongo-marsupial:27018,mongo-meerkat:27019/?replicaSet=rs0" --db data --collection aliases --file ./importData/data.aliases-PRD.json --jsonArray`
- `sudo mongoimport --uri "mongodb://mongo-monkey:27017,mongo-marsupial:27018,mongo-meerkat:27019/?replicaSet=rs0" --db data --collection currencies --file ./importData/data.currencies-PRD.json --jsonArray`
- `sudo mongoimport --uri "mongodb://mongo-monkey:27017,mongo-marsupial:27018,mongo-meerkat:27019/?replicaSet=rs0" --db data --collection creatures --file ./importData/data.creatures-PRD.json --jsonArray`
- `sudo mongoimport --uri "mongodb://mongo-monkey:27017,mongo-marsupial:27018,mongo-meerkat:27019/?replicaSet=rs0" --db data --collection emojis --file ./importData/data.emojis.json --jsonArray`
- `sudo mongoimport --uri "mongodb://mongo-monkey:27017,mongo-marsupial:27018,mongo-meerkat:27019/?replicaSet=rs0" --db data --collection profanities --file ./importData/data.profanities.json --jsonArray`