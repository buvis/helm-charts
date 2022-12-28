# Supabase

This directory contains the configurations and scripts required to run Supabase inside a Kubernetes cluster. It was taken from pieveee/supabase-kubernetes branch feature/jwt-secrets, because supabase-community haven't merged the pull request yet and I wanted to have some usable chart.

## Disclamer

We use [supabase/postgres](https://hub.docker.com/r/supabase/postgres) to create and manage the Postgres database. This permit you to use replication if needed but you'll have to use the Postgres image provided Supabase or build your own on top of it. You can also choose to use other databases provider like [StackGres](https://stackgres.io/) or [Postgres Operator](https://github.com/zalando/postgres-operator).

For the moment we are using a root container to permit the installation of the missing `pgjwt` and `wal2json` extension inside the `initdbScripts`. This is considered a security issue, but you can use your own Postgres image instead with the extension already installed to prevent this. We provide an example of `Dockerfile`for this purpose, you can use [ours](https://hub.docker.com/r/tdeoliv/supabase-bitnami-postgres) or build and host it on your own.

The database configuration we provide is an example using only one master. If you want to go to production, we highly recommend you to use a replicated database.

## Quickstart

> For this section we're using Minikube and Docker to create a Kubernetes cluster

```bash
# Clone Repository
git clone https://supabase-community.github.io/supabase-kubernetes

# Switch to charts directory
cd supabase-kubernetes/charts/supabase/

# Create JWT secret
kubectl -n default create secret generic demo-supabase-jwt \
  --from-literal=anonKey='eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYW5vbiIsImlhdCI6MTY0MDMwMDQwMCwiZXhwIjoxNzk4MDY2ODAwfQ.JaEiRNdyxX3Pk6XupxauDazXeadLTgTHz5cV7joUrQE' \
  --from-literal=serviceKey='eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoic2VydmljZV9yb2xlIiwiaWF0IjoxNjQwMzAwNDAwLCJleHAiOjE3OTgwNjY4MDB9.sUJPVrhMsSaLgizyCWIgNOIRmjavxDB4Lm3hzb4dC5U' \
  --from-literal=secret='abcdefghijklmnopqrstuvwxyz123456'

# Create SMTP secret
kubectl -n default create secret generic demo-supabase-smtp \
  --from-literal=username='your-mail@example.com' \
  --from-literal=password='example123456'

# Create DB secret
kubectl -n default create secret generic demo-supabase-db \
  --from-literal=username='postgres' \
  --from-literal=password='example123456'

# Install the chart
helm -n default install demo -f values.example.yaml .
```

The first deployment can take some time to complete (especially auth service). You can view the status of the pods using:

```bash
kubectl -n default get pod

NAME                                      READY   STATUS    RESTARTS      AGE
demo-supabase-auth-78547c5c8d-chkbm       1/1     Running   2 (40s ago)   47s
demo-supabase-db-5bc75fbf56-4cxcv         1/1     Running   0             47s
demo-supabase-kong-8c666695f-5vzwt        1/1     Running   0             47s
demo-supabase-meta-6779677c7-s77qq        1/1     Running   0             47s
demo-supabase-realtime-6b55986d7d-csnr7   1/1     Running   0             47s
demo-supabase-rest-5d864469d-bk5rv        1/1     Running   0             47s
demo-supabase-storage-6c878dcbd4-zzzcv    1/1     Running   0             47s
```

### Tunnel with Minikube

When the installation will be complete you'll be able to create a tunnel using minikube:

```bash
minikube tunnel
```

If you just use the `value.example.yaml` file, you can access the API or the Studio App using the following endpoints:

- <http://api.localhost>
- <http://studio.localhost>

### Uninstall

```Bash
# Uninstall Helm chart
helm -n default uninstall demo

# Delete secrets
kubectl -n default delete secret demo-supabase-db
kubectl -n default delete secret demo-supabase-jwt
kubectl -n default delete secret demo-supabase-smtp
```

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

## How to use in Production

We didn't provide a complete configuration to go production because of the multiple possibility.

But here are the important points you have to think about:

- Use a replicated version of the Postgres database.
- Add SSL to the Postgres database.
- Add SSL configuration to the ingresses endpoints using either the `cert-manager` or a LoadBalancer provider.
- Change the domain used in the ingresses endpoints.
- Generate a new secure JWT Secret.

## Troubleshooting

### Ingress Controller and Ingress Class

Depending on your Kubernetes version you might want to fill the `className` property instead of the `kubernetes.io/ingress.class` annotations. For example:

```yml
kong:
  ingress:
    enabled: 'true'
    className: "nginx"
    annotations:
      nginx.ingress.kubernetes.io/rewrite-target: /
```
