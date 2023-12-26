# fb-web API

Endpoint: `https://www.facebook.com/api/graphql/`  
Type: GraphQL-Relay endpoint  
Content-Type: `application/x-www-form-urlencoded`

## Headers

### Cookies

| Name       | Value                                                                                  |
| ---------- | -------------------------------------------------------------------------------------- |
| `c_user`   | User ID                                                                                |
| `datr`     | Browser identifier: obtain this via POSTing `https://www.facebook.com/cookie/consent/` |
| `fr`       | Ad identifier, not required                                                            |
| `m_ls`     | Possibly messenger related, see module `LSCookie` (TODO!)                              |
| `presence` | "C" + `JSON.stringify({"t3": [], "utc3": +new Date(), "v": 1})`                        |
| `ps_l`     | 0 (not required)                                                                       |
| `ps_n`     | 0 (not required)                                                                       |
| `sb`       | Site integrity/anti-fraud cookie, also set by accepting cookie consent                 |
| `usida`    | JWT header with id, ver, time                                                          |
| `wd`       | Window Dimensions (`window.innerWidth`x`window.innerHeight`)                           |
| `xs`       | **Auth token, detailed below**                                                         |

<details>
<summary>
Most cookie names with their expiry times and SameSite attributes (taken from CookieCoreConfig)
</summary>

```json
{
  "c_user": { "t": 31536000, "s": "None" },
  "cppo": { "t": 86400, "s": "None" },
  "dpr": { "t": 604800, "s": "None" },
  "fbl_ci": { "t": 31536000, "s": "None" },
  "fbl_cs": { "t": 31536000, "s": "None" },
  "fbl_st": { "t": 31536000, "s": "Strict" },
  "i_user": { "t": 31536000, "s": "None" },
  "locale": { "t": 604800, "s": "None" },
  "m_ls": { "t": 34560000, "s": "None" },
  "m_pixel_ratio": { "t": 604800, "s": "None" },
  "noscript": { "s": "None" },
  "presence": { "t": 2592000, "s": "None" },
  "sfau": { "s": "None" },
  "usida": { "s": "None" },
  "vpd": { "t": 5184000, "s": "Lax" },
  "wd": { "t": 604800, "s": "Lax" },
  "wl_cbv": { "t": 7776000, "s": "None" },
  "x-referer": { "s": "None" },
  "x-src": { "t": 1, "s": "None" }
}
```

</details>

#### The `xs` (auth) cookie

This cookie contains colon(:)-delimited values, in order:

- session ID
- session secret
- session mode ("remember me" or not)
- session creation date
- -1 (?)
- some sort of ID/flag (?)
- (empty) (?)
- tx secret (???)

The expiry date of this cookie is intentionally set to a lower value than the current timestamp.

## Request payload

Format: `application/x-www-form-urlencoded`

| Parameter                  | Value                                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------------- |
| `av`                       | Logged-in user ID                                                                            |
| `__user`                   | Same as above                                                                                |
| `__a`                      | 1 (constant)                                                                                 |
| `__req`                    | Request identifier, two-char, incremental code composed from the charset `0-9a-z`            |
| `__hs`                     | "Haste session" version, constant: `19716.HYP:comet_pkg.2.1..2.1` (`SiteData.haste_session`) |
| `dpr`                      | 2 (constant), from `SiteData.pr`                                                             |
| `__ccg`                    | Connection class, get from `WebConnectionClassServerGuess.connectionClass`                   |
| `__rev`                    | Client revision constant, `SiteData.client_revision`                                         |
| `__s`                      | Web session ID `WebSession.getId()`                                                          |
| `__hsi`                    | Haste session ID `SiteData.hsi`                                                              |
| `__dyn`                    | (TODO) `ServerJSDefine.getLoadedModuleHash()`                                                |
| `__csr`                    | (TODO) `CSRBitMap.toCompressedString()` :warning: This value is modified by .set at runtime! |
| `__comet_req`              | (TODO) `SiteData.comet_env` OR 1 (constant)                                                  |
| `fb_dtsg`                  | (TODO) `DTSGInitialData.token`                                                               |
| `jazoest`                  | Related to `fb_dtsg` and can be obtained via `DTSGUtils.getNumericValue([fb_dtsg value])`    |
| `lsd`                      | LSD Token `LSD.token` (seems constant?)                                                      |
| `__aaid`                   | Ad account ID, usually 0, `CurrentAdAccount.getID()`                                         |
| `__spin_r`                 | (TODO) `SiteData.__spin_r`                                                                   |
| `__spin_b`                 | (TODO) `SiteData.__spin_b`                                                                   |
| `__spin_t`                 | (TODO) `SiteData.__spin_t`                                                                   |
| `fb_api_caller_class`      | `RelayModern` (constant)                                                                     |
| `fb_api_req_friendly_name` | **Relay query name (eg. `CometHovercardQueryRendererQuery`)**                                |
| `encrypted_variables`      | **Encrypted GraphQL/Relay variable list, JSONObject, key-value pairs (TODO!)**               |
| `variables`                | **GraphQL/Relay variable list, JSONObject, key-value pairs**                                 |
| `doc_id`                   | **GraphQL/Relay document identifier**                                                        |
| `server_timestamps`        | `true` (constant)                                                                            |

<!-- TODO: Document modules and relevant vendor JS code -->
