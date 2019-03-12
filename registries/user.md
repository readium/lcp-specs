[![Readium Logo](https://readium.org/assets/logos/readium-logo.png)](https://readium.org)

# Readium User Fields Registry


| Name | Value | Format | Required? |
| ---- | ----- | ------ | --------- |
| `id` | Unique identifier for the User at a specific Provider | String | No, but highly recommended |
| `email` | The User’s e-mail address | String | No |
| `name` | The User’s name | String | No |
| `encrypted` | A list of which user object values are encrypted in this License Document | Array of one or more strings matching the above names or an extension | Yes, if encryption is used for any field |