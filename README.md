# Connect Cadence to DataStax Astra using cql-proxy⚠️

⚠️Under Construction

## Prerequisites
1. Create a [DataStax Astra](https://auth.cloud.datastax.com/auth/realms/CloudUsers/protocol/openid-connect/registrations?client_id=auth-proxy&response_type=code&scope=openid+profile+email&redirect_uri=https://astra.datastax.com/welcome&) account
2. Create a database
3. Add two keyspaces `cadence` and `cadence_visibility` to the database you created via "Add Keyspace"
  ![image](https://user-images.githubusercontent.com/3710715/161330020-ad4ca2ac-bbad-427c-9d76-b2845f8d740e.png)
5. Create an [Astra token](https://docs.datastax.com/en/astra/docs/manage-application-tokens.html)
6. Obtain your Database ID
  * DB identifer is the last ID in the URL when your DB is selected (the "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" part)
    * `https://astra.datastax.com/org/.../database/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
  * Or the "Datacenter ID" without the `-1` at the end (you'll have to copy and remove that trailing `-1`)
    ![image](https://user-images.githubusercontent.com/3710715/161331138-906f4f7f-919e-4f47-a731-a855d54369c5.png)
    
 ## Setup
 1. Clone this repo
```sh
git clone https://github.com/melienherrera/cadence-astra-cql-proxy.git
cd cadence-astra-cql-proxy
```
2. Update `.env` with your Astra token and DB identifier
```yaml
# Update these
ASTRA_TOKEN=updateme
ASTRA_DATABASE_ID=updateme
```
3. Migrate the Cadence schema to your Astra database by running the following commands:
```
docker-compose -f docker-compose-schema.yaml run cadence \
  -ep cqlproxy-cadence -k cadence setup-schema -v 0.0
docker-compose -f docker-compose-schema.yaml run cadence \
  -ep cql-proxy -k cadence update-schema -d schema/cassandra/cadence/versioned/

docker-compose -f docker-compose-schema.yaml run cadence \
  -ep cql-proxy -k cadence_visibility setup-schema -v 0.0
docker-compose -f docker-compose-schema.yaml run cadence \
  -ep cql-proxy -k cadence_visibility update-schema -d schema/cassandra/visibility/versioned/
```
4. Start up Cadence
```
docker-compose -f docker-compose-cqlproxy.yml up
```
Check Cadence UI at http://localhost:8088/

## Test and Validate
1. Make sure your Cadence Server is up and running
2. Install the [Cadence CLI Tool](https://cadenceworkflow.io/docs/cli/#using-the-cli) and register a domain by running:
```
cadence --do samples-domain d re 
```
3. Clone the [cadence-samples](https://github.com/uber-common/cadence-samples) repository and follow the steps to run the sample HelloWorld project
