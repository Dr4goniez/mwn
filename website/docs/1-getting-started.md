# Getting started

Installation: `npm install mwn`

Importing mwn:

In JavaScript:

```js
const { Mwn } = require('mwn');
```

Note: Prior to mwn v2.0.0, import was via `const { mwn } = require('mwn')`. Prior to v0.8.0, it was via `const mwn = require('mwn');`

In TypeScript:

```ts
import { Mwn } from 'mwn';
```

If you're migrating from mwbot, note that:

- `edit` in mwbot is different from `edit` in mwn. You want to use `save` instead.
- If you were using the default formatversion=1 output format, set formatversion: 1 in the config options.

#### Set up a bot password or OAuth credentials

Mwn supports authentication via both [BotPasswords](https://www.mediawiki.org/wiki/Manual:Bot_passwords) and [OAuth](https://www.mediawiki.org/wiki/OAuth/Owner-only_consumers). Use of OAuth is recommended as it does away the need for separate API requests for logging in, and is also more secure. Both OAuth versions, 1.0a and 2, are supported.

Bot passwords, however, are a bit easier to set up. To generate one, go to the wiki's [Special:BotPasswords](https://en.wikipedia.org/wiki/Special:BotPasswords) page.

#### Initializing bot object

```js
const bot = await Mwn.init({
	apiUrl: 'https://en.wikipedia.org/w/api.php',

	// Can be skipped if the bot doesn't need to sign in
	username: 'YourBotUsername',
	password: 'YourBotPassword',

	// Instead of username and password, you can use OAuth 2 to authenticate (recommended),
	// if the wiki has Extension:OAuth enabled
	OAuth2AccessToken: "YouOAuth2AccessToken",

	// Or use OAuth 1.0a (also only applicable for wikis with Extension:OAuth)
	OAuthCredentials: {
		consumerToken: '16_DIGIT_ALPHANUMERIC_KEY',
		consumerSecret: '20_DIGIT_ALPHANUMERIC_KEY',
		accessToken: '16_DIGIT_ALPHANUMERIC_KEY',
		accessSecret: '20_DIGIT_ALPHANUMERIC_KEY'
	},

	// Set your user agent (required for WMF wikis, see https://meta.wikimedia.org/wiki/User-Agent_policy):
	userAgent: 'myCoolToolName 1.0 ([[link to bot user page or tool documentation]])',

	// Set default parameters to be sent to be included in every API request
	defaultParams: {
		assert: 'user' // ensure we're logged in
	}
});
```

This creates a bot instance, signs in and fetches tokens needed for editing. (If credentials for multiple authentication methods are provided, OAuth 2 takes precedence, followed by OAuth 1.0a and bot passwords.)

You can also create a bot instance synchronously (without using await):

```js
const bot = new Mwn({
	...options
});
```

This creates a bot instance which is not signed in. Then to authenticate, use `bot.login()` which returns a promise. If using OAuth, use `bot.initOAuth()` followed by `bot.getTokensAndSiteInfo()`. Note that `bot.initOAuth()` does not involve an API call. Any error in authentication will surface on running bot.getTokensAndSiteInfo().

The bot options can also be set using `setOptions` rather than through the constructor:

```js
bot.setOptions({
	silent: false, 		// suppress messages (except error messages)
	retryPause: 5000, 	// pause for 5000 milliseconds (5 seconds) on maxlag error.
	maxRetries: 3 		// attempt to retry a failing requests upto 3 times
});
```
