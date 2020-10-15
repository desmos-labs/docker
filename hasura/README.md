# Running a Hasura container
In order to run a Hasura GraphQL endpoint, the following command should be used: 

```shell
docker run -d --expose 8080 \
  -e HASURA_GRAPHQL_DATABASE_URL=<DB_URL> \
  -e HASURA_GRAPHQL_ENABLE_CONSOLE=true \
  -e HASURA_GRAPHQL_ACCESS_KEY=<SECRET_KEY> \
  -e HASURA_GRAPHQL_UNAUTHORIZED_ROLE=anonymous \
  --name hasura \
  --restart always \
  hasura/graphql-engine:latest
``` 

- `DB_URL` should be the full URL containing the details to connect to your PostgreSQL database instance. It should be formatted as `user:password@host:port/db_name`
- `SECRET_KEY` should be the secret key used to access the console of Hasura

## Unauthorized role
By using the `HASURA_GRAPHQL_ACCESS_KEY` environmental variable, you will enable the protection for the Hasura console, which is **always recommended**. However, this will require your applications to use the `x-hasura-admin-secret/x-hasura-access-key` header when performing requests to your APIs. 

Instead of doing that, which would expose your private key to everyone, you should instead use [unathorized roles](https://hasura.io/docs/1.0/graphql/core/auth/authentication/unauthenticated-access.html) to expose your APIs to the public by maintain your console private.

You can read more about this topic inside [Hasura's Access Control documentation](https://hasura.io/docs/1.0/graphql/core/auth/authorization/index.html).

## Adding SSL
As any other Docker container, you can add an SSL protection to any Hasura container by following the [SSL guide](../ssl). 

Just remember that, by default, **Hasura containers expose the `8080` port**.
