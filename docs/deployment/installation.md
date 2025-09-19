## Quick Installation

1) Install [Node.js](https://nodejs.org/) for your operating system.

2) Clone the [ATON](https://github.com/phoenixbf/aton) repository locally.

3) Install (or update) required modules from main ATON folder by typing:
```
npm install
```

4) After installation, clone this repository inside the /wapps directory. This is located directly under ATON.

5) Deploy ATON *main service* on local machine simply using:
```
npm start
```

## Load Scene

1) After ATON is deployed, load a scene with the following url parameters:
```
http://localhost:8080/a/thoth_v2/?s=(scene_id)
```
This will open the selected scene using the THOTH web app.