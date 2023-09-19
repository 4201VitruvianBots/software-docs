# Progressive Web Apps Documentation

## What Progressive Web Apps (PWAs) are

A Progressive Web App is like a website that can be installed like a mobile/desktop app. It can have *service workers* which provide additional functionality compared to a website. Installing a PWA will add a home screen or desktop icon.

## Why they are useful for the scouting app

The most useful feature of PWAs for us is that service workers can cache resources, meaning that the PWA can be used offline. This means that we can serve the scouting app from the backend so that we can push updates immediately, but losing connection will not prevent scouters from collecting data.

Additionally, having a home screen shortcut makes it more convenient for scouters to find the app.

## Installation and Configuration

### Using CRA

Create React App automatically sets it up as a PWA, but no service workers are configured. See https://create-react-app.dev/docs/making-a-progressive-web-app/ for information on how to set it up.

### Using Vite

Vite does not set up the app as a PWA by default, but the functionality can be added with [`vite-plugin-pwa`](https://www.npmjs.com/package/vite-plugin-pwa). Use

```bash
npm install vite-plugin-pwa
```

Then refer to https://vite-pwa-org.netlify.app/guide/ for configuration instructions.

## External References

https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps  
https://vite-pwa-org.netlify.app/guide/  
https://create-react-app.dev/docs/making-a-progressive-web-app/  
https://web.dev/learn/pwa/  
