# Run with PM2

PM2 keeps the ATON service running under a process manager. Complete the [native ATON deployment](installation_basic.md), including the optional Exact Geodesic setup if required, before using this page.

Install PM2 globally:

```sh
npm install --global pm2
```

From the ATON repository root, start the checked-in ecosystem configuration:

```sh
pm2 start ecosystem.config.js
```

Useful operational commands are:

```sh
pm2 status
pm2 logs
pm2 restart all
pm2 stop all
```

PM2 does not change THOTH's deployment mode, API routes, storage, or authentication. Those remain controlled by the ATON checkout and THOTH configuration. Use your operating system's PM2 startup integration if the service must return after a machine reboot.
