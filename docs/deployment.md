# Portfolio deployment

The [public portfolio](https://fleet-mind-portfolio.vercel.app/) is a Next.js production deployment on Vercel. The public UI displays recorded scores, fictional assets, precomputed SHAP, replayed simulator telemetry, and scripted Copilot responses. The full workspace runs locally with authenticated PostgreSQL and private Copilot conversations. The optional Render configuration below is for a separate stateless inference service. [Public validation](release-validation.md#public-deployment)

## Publication source

Deploy `main` from [WaleedMatrix/FleetMind-Portfolio](https://github.com/WaleedMatrix/FleetMind-Portfolio). Its history starts with the reviewed source import. The original development repository stays private. [Release security audit](security-release.md)

## Vercel frontend

1. To reproduce the deployment, import the publication repository as a Next.js project with **Root Directory** set to `frontend` and Node.js 22. The checked-in [configuration](../frontend/vercel.json) runs `npm ci` and `npm run build`.
2. Set `NEXT_PUBLIC_FLEETMIND_MODE=portfolio` in Production and any public Preview environment. This selects the profile at build time. Leave `FLEETMIND_API_URL` and `FLEETMIND_PORTFOLIO_API_URL` unset for the recorded deployment.
3. In a fresh browser session, check the dashboard, asset detail, alert investigation, SHAP, Model Intelligence and Model Health, Copilot examples, and telemetry replay at desktop and phone widths. Confirm the recorded scores message on Architecture.
4. Verify that `GET /api/fleetmind/auth/me`, `POST /api/fleetmind/auth/login`, and `POST /api/fleetmind/predict` return 404. Match the GitHub Actions run and the Vercel deployment status to the publication commit SHA.

[Vercel Hobby](https://vercel.com/docs/plans/hobby) · [Project root configuration](https://vercel.com/docs/monorepos) · [Environment variables](https://vercel.com/docs/environment-variables)

## Optional Render inference service

1. Review [render.yaml](../render.yaml), [Dockerfile.portfolio](../Dockerfile.portfolio) and the [dependency audit](security-release.md). In your Free workspace create a **Blueprint** from the clean repository, or create a Docker **Web Service** with the same settings. Select the **Free** instance, repository root build context, Dockerfile `./Dockerfile.portfolio`, health check `/health`, and turn off automatic redeploys. The container reads Render's `PORT`.
2. Add no database, persistent disk, model credentials, environment secrets or worker. Test `GET /health` and a known simulator input at `POST /infer` before configuring Vercel. Direct calls share the same 30-attempt-per-minute process budget, with one inference slot and a 2 KiB upload limit. A low ceiling is intentional.
3. In a separate test deployment, set server-only `FLEETMIND_PORTFOLIO_API_URL` to the service's exact HTTPS origin and redeploy. In Architecture, try **Score this reading**, including after an idle period. Cold starts can exceed the bridge deadline and the recorded score remains available. Review the provider's current limits before provisioning. [Render Free](https://render.com/docs/free) · [Local inference measurements](release-validation.md#optional-stateless-inference)

[Render Docker support](https://render.com/docs/docker) · [Blueprint configuration](https://render.com/docs/blueprint-spec)

## Local reproduction

~~~powershell
$env:NEXT_PUBLIC_FLEETMIND_MODE = 'portfolio'
Set-Location frontend
npm.cmd ci
npm.cmd run build
npm.cmd run start -- --hostname 127.0.0.1 --port 3000
~~~

For optional loopback inference, use the Git root in a separate terminal. The CPU and memory limits reproduce the local test conditions.

~~~powershell
docker build -f Dockerfile.portfolio -t fleetmind-portfolio .
docker run --rm --name fleetmind-portfolio-dev --cpus=0.1 --memory=512m --memory-swap=512m --read-only --tmpfs /tmp:size=16m -p 127.0.0.1:19983:8000 fleetmind-portfolio
~~~

Set `FLEETMIND_PORTFOLIO_API_URL=http://127.0.0.1:19983` before the portfolio build and start. The proxy accepts an exact Render HTTPS origin or this loopback form. The hosted configuration ships precomputed SHAP and does not install the local SHAP stack for live requests.

## Release approval

After owner approval, tag the exact publication commit verified by GitHub Actions and Vercel. Use the [v1.0.0 notes](../CHANGELOG.md) for the release entry.

~~~powershell
$verifiedCommit = Read-Host 'Verified publication commit SHA'
git tag -a v1.0.0 $verifiedCommit -m "FleetMind v1.0.0"
git push origin v1.0.0
~~~
