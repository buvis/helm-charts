# Supabase

This directory contains the configurations and scripts required to run Supabase inside a Kubernetes cluster. It was taken from pieveee/supabase-kubernetes branch feature/jwt-secrets, because supabase-community haven't merged the pull request yet and I wanted to have some usable chart.

## Disclamer

We use [supabase/postgres](https://hub.docker.com/r/supabase/postgres) to create and manage the Postgres database. This permit you to use replication if needed but you'll have to use the Postgres image provided Supabase or build your own on top of it. You can also choose to use other databases provider like [StackGres](https://stackgres.io/) or [Postgres Operator](https://github.com/zalando/postgres-operator).

For the moment we are using a root container to permit the installation of the missing `pgjwt` and `wal2json` extension inside the `initdbScripts`. This is considered a security issue, but you can use your own Postgres image instead with the extension already installed to prevent this. We provide an example of [Dockerfile](https://github.com/supabase-community/supabase-kubernetes/blob/main/charts/supabase/docker/Database.Dockerfile) for this purpose, you can use [ours](https://hub.docker.com/r/supabase/postgres) or build and host it on your own.

The database configuration we provide is an example using only one master. If you want to go to production, we highly recommend you to use a replicated database.

## Customize

You should consider to adjust the following values in `values.yaml`:

- `RELEASE_NAME`: Name used for helm release
- `NAMESPACE`: Namespace used for the helm release
- `API.EXAMPLE.COM` URL to Kong API
- `STUDIO.EXAMPLE.COM` URL to Studio

If you want to use mail, consider to adjust the following values in `values.yaml`:

- `SMTP_ADMIN_MAIL`
- `SMTP_HOST`
- `SMTP_PORT`
- `SMTP_SENDER_NAME`

### Secrets

You can use a Kubernetes secret to store JWT keys, SMTP and database credentials. Then reference the name of the secret in `values.yaml` in `secretName` key.

The secret can be created with kubectl via command-line:

```bash
kubectl -n NAMESPACE create secret generic RELEASE_NAME \
  --from-literal=jwtSecret='JWT_TOKEN_AT_LEAST_32_CHARACTERS_LONG' \
  --from-literal=jwtAnonKey='JWT_ANON_KEY' \
  --from-literal=jwtServiceKey='JWT_SERVICE_KEY \'
  --from-literal=smtpUsername='SMTP_USER' \
  --from-literal=smtpPassword='SMTP_PASSWORD \\'
  --from-literal=dbUsername='DB_USER' \
  --from-literal=dbPassword='PW_USER'
```

> 32 characters long secret can be generated with `openssl rand 64 | base64`
> You can use the [JWT Tool](https://supabase.com/docs/guides/hosting/overview#api-keys) to generate anon and service keys.
