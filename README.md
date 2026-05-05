# n8n on Docker & SAP Kyma

Deploy [n8n](https://n8n.io) — the open-source workflow automation tool — either locally with Docker Compose or on [SAP Kyma](https://kyma-project.io) (a Kubernetes-based runtime).

## Repository Structure

```
.
├── docker-compose.yml   # Local deployment via Docker Compose
├── n8n-kyma.yaml        # Kubernetes Deployment for SAP Kyma
├── n8n-pvc.yaml         # PersistentVolumeClaim for n8n data
└── .env.example         # Environment variable template
```

---

## Option 1 — Run Locally with Docker Compose

### Prerequisites

- [Docker](https://docs.docker.com/get-docker/) installed

### Setup

1. Copy the environment template and fill in your encryption key:

   ```bash
   cp .env.example .env
   ```

   Generate a secure key with:

   ```bash
   openssl rand -hex 32
   ```

2. Start n8n:

   ```bash
   docker compose up -d
   ```

3. Open [http://localhost:5678](http://localhost:5678) in your browser.

n8n data is persisted in the `./n8n_data` directory.

---

## Option 2 — Deploy on SAP Kyma

### Prerequisites

- A running [SAP BTP Kyma environment](https://help.sap.com/docs/btp/sap-business-technology-platform/kyma-environment)
- `kubectl` configured to point at your Kyma cluster

### Setup

1. Create the namespace:

   ```bash
   kubectl create namespace n8n
   ```

2. Edit `n8n-kyma.yaml` and replace all placeholder values:

   | Placeholder | Description |
   |---|---|
   | `<your-encryption-key>` | Random 32-byte hex string (`openssl rand -hex 32`) |
   | `<your-kyma-domain>` | Your Kyma app domain, e.g. `n8n.abc1234.kyma.ondemand.com` |

3. Apply the manifests:

   ```bash
   kubectl apply -f n8n-pvc.yaml
   kubectl apply -f n8n-kyma.yaml
   ```

4. Verify the deployment:

   ```bash
   kubectl get pods -n n8n
   ```

> **Note:** This setup uses a `PersistentVolumeClaim` (5 Gi) to store n8n data across pod restarts.

---

## Security Notes

- **Never commit real encryption keys** to version control. Use `.env` locally (it is listed in `.gitignore`) and Kubernetes Secrets for production.
- The `N8N_ENCRYPTION_KEY` encrypts credentials stored by n8n — losing it means losing access to all saved credentials.

---

## License

MIT
