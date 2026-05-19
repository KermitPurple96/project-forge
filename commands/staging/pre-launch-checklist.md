# 🔀 Command: pre-launch-checklist

> Final verification before going to production.

## Checklist

### Security
- [ ] SSL/TLS configured (HTTPS only)
- [ ] Security headers set (CSP, HSTS, X-Frame-Options)
- [ ] CORS configured for production domains only
- [ ] Rate limiting enabled
- [ ] All secrets are in env vars (not hardcoded)
- [ ] Debug mode disabled
- [ ] Error messages don't leak internal details
- [ ] Security review passed

### Infrastructure
- [ ] DNS configured and propagated
- [ ] CDN configured for static assets
- [ ] Database backups automated
- [ ] Monitoring and alerting set up
- [ ] Error tracking configured (Sentry, etc.)
- [ ] Log aggregation configured

### Legal / compliance
- [ ] Privacy policy published
- [ ] Terms of service published
- [ ] Cookie consent (if EU users)
- [ ] GDPR data deletion flow works (if applicable)
- [ ] Accessibility meets target WCAG level

### Content
- [ ] All placeholder/lorem ipsum removed
- [ ] Favicon and meta tags set
- [ ] OG/social sharing images configured
- [ ] 404 page customized
- [ ] Email templates working (welcome, reset, etc.)

### Performance
- [ ] Images optimized (WebP, proper sizes)
- [ ] Assets minified and compressed
- [ ] Lighthouse score acceptable (>80 all categories)
- [ ] Core Web Vitals passing
