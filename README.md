# README

This is the MMM-T3 web service for getting and updating refresh tokens for use with your Tesla Fleet API app and [MMM-Tesla3](https://github.com/usncahill/MMM-Tesla3) module. This was forked from the [Express](https://expressjs.com) [Hello world](https://expressjs.com/en/starter/hello-world.html) example on [Render](https://render.com). This app can be deployed on a free-tier web service at [https://render.com](https://render.com).

## References
* [Deploying a Node Express app (render.com docs)](https://render.com/docs/deploy-node-express-app)
* [Tesla Developer](https://developer.tesla.com/)
* [Tesla Fleet API Getting Started](https://developer.tesla.com/docs/fleet-api/getting-started/what-is-fleet-api)

## Deployment
1. Fork this repo.
2. Get a free web service account. The following directions will be based on replicating my work on render.com. [Render account and web service setup](https://render.com/docs/your-first-deploy). Consider the following during setup:
   * Name your service: omit "Tesla" from the name. the name will be part of your URL and Tesla does not allow app names or URLs that contain Tesla in the name. You can name your render web app anything as long as the name is available and can recreate the web service in only a few clicks, so don't worry about this, you can delete the service and remake with new name later.
   * Choose instance type: Recommend the free one. You will only need this to register app and refresh tokens. MMM-Tesla3 does not directly utilize the web service.
   * Add environment variables: will return to these in a moment
     * CLIENT_SECRET: from Tesla Dev dashboard
     * CLIENT_ID: from Tesla Dev dashboard
     * PASSWORD: make up a sufficiently complex password so other users cant charge their activity to your app (i.e. your client_secret/id). on render.com, the field offers to auto-populate a long suitable string. use that and copy it for later.

3. Create your Tesla Fleet API app using their [getting started directions here](https://developer.tesla.com/docs/fleet-api/getting-started/what-is-fleet-api), noting the following:
  * **[Tesla Getting Started Step 2](https://developer.tesla.com/docs/fleet-api/getting-started/what-is-fleet-api)**: Use the following in your app inputs:
    * Name: This should match your render app name from earlier.
    * Allowed Origin(s): \<name\>.onrender.com
    * Allowed Redirect URI(s): \<name\>.onrender.com/auth/callback
    * Allowed Returned URL(s): \<name\>.onrender.com
    * Client credentials: client-credentials, authorization-code
    * Scopes: vehicle info, location, commands, charging
  * **[Tesla Getting Started Step 2](https://developer.tesla.com/docs/fleet-api/getting-started/what-is-fleet-api)**: after you are done outlining your app in Tesla dev, copy your client ID and secret from the [Tesla Dev dashboard](https://developer.tesla.com/dashboard) into the environment variables on render.com.
  * **[Tesla Getting Started Step 3](https://developer.tesla.com/docs/fleet-api/getting-started/what-is-fleet-api)**: this makes your public key
    * `openssl ecparam -name prime256v1 -genkey -noout -out private-key.pem`
    * `openssl ec -in private-key.pem -pubout -out public-key.pem`
    * replaces the `mmm-t3-service pem` in your forked repo under `.well-known/appspecific/com.tesla.3p.public-key.pem`
    * ensure your repo is updated on the server where your app resides so this key is avaiable for the register step next.
  * **[Tesla Getting Started Step 4](https://developer.tesla.com/docs/fleet-api/getting-started/what-is-fleet-api)**: use the links in the directions:
    * PARTNER auth token link: run curl using their example (heavy editting) in the command line. They will respond in the terminal with your registration auth token which will be used next.
    * REGISTER endpoint step: run one of the multiple linked options. From what I can tell, you dont need the response from this.

Finally we are at: **Why you need my app** aka, authorizing your app to do stuff for you, aka being your own customer. See [Tesla API Third Party tokens](https://developer.tesla.com/docs/fleet-api/authentication/third-party-tokens) for what my app is mimicking.

4. Goto \<name\>.onrender.com/auth?password=\<your PASSWORD from earlier here\>
    * MMM-T3-service will redirect you to login to Tesla and give your app permissions. You should see all your own Tesla app stuff (not mine).
    * Tesla should redirect back to \<name\>.onrender.com/auth/callback.
    * MMM-T3-service should post the returned code to Tesla's refresh token path.
    * Tesla should respond to the post with a token json.
    * MMM-T3-service will spit out a page with the token json content. If using this for MMM-Tesla3, copy all the token text including the brackets (e.g. {token things here, copy us and the brackets}) and paste to the token.json per the MMM-Tesla3 directions.

## Future
Things I'm thinking about adding/changing
    * Add automation for the PARTNER and REGISTER steps. Though, this is a one time thing, not everyone wants to use curl or whatever.
    * Add page fields and buttons instead of relying on variables and query params. 
