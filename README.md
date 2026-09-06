# Natali Wisher portfolio

Initial responsive Coming Soon page for a UX/UI and product design portfolio with three future case studies.

## Site

`dist/index.html` is the complete dependency-free page. It uses a native Apple system-font stack, CSS glass surfaces, responsive layout, and reduced-motion support.

## AWS

The production assets are in a private, encrypted, versioned S3 bucket. CloudFront accesses objects through signed Origin Access Control and redirects HTTP to HTTPS. Exact resource identifiers and certificate validation records are recorded in `infra/deployment.json`.

AWS URL: https://d2ebj1pzhgdr1.cloudfront.net/

## Remaining setup

1. Source repository: https://github.com/wisheradam/Natali-wisher-product-designer (main).
2. Authenticate to Dynadot and inspect existing DNS records before making changes. Preserve mail and unrelated records.
3. Add both ACM validation CNAMEs from `infra/deployment.json`.
4. After ACM becomes ISSUED, attach the certificate to the existing CloudFront distribution, with aliases `nataliwisher.com` and `www.nataliwisher.com`, SNI and TLS 1.2 or newer.
5. Point `www` to the CloudFront domain. Use Dynadot apex ALIAS if supported, otherwise arrange Route53 DNS while preserving existing records. Do not use a fixed CloudFront IP address.
6. GitHub Actions automatically deploys changes to dist/ on main using NataliPortfolioGitHubDeploy, an OIDC role scoped to this repository and branch. No long-lived AWS access keys. Workflow includes CDN invalidation and exact content verification.
7. Verify both custom HTTPS domains and exact deployed content.

## Manual content deployment

With an authenticated AWS CLI session:

```sh
aws s3 cp dist/index.html s3://nataliwisher-portfolio-533267008693/index.html --content-type 'text/html; charset=utf-8' --cache-control 'public,max-age=300' --region us-east-1
aws cloudfront create-invalidation --distribution-id E1LBCAP3SYQ78D --paths '/index.html' '/'
```

Do not create duplicate infrastructure when resuming. Reuse the recorded distribution, bucket and certificate.
