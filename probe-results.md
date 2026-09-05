
## 2026-09-03 11:40:30 UTC


## 2026-09-03 14:23:25 UTC
https://beta-apis.alfaview.com/v2/languages -> HTTP 401
https://beta-apis.alfaview.com/v2/languages` -> HTTP 404
https://apis.alfaview.com/v2/languages` -> HTTP 404

## 2026-09-03 15:21:47 UTC
https://beta-apis.alfaview.com/v2/languages` -> HTTP 404
https://apis.alfaview.com/v2/languages` -> HTTP 404
https://beta-apis.alfaview.com/openapi.json -> HTTP 404
https://apis.alfaview.com/openapi.json -> HTTP 404
https://beta-apis.alfaview.com/openapi.json` -> HTTP 404
https://apis.alfaview.com/openapi.json` -> HTTP 404
https://demo-company.alfaview.com/ -> 200 len=1381
https://demo-company.alfaview.com/api/v1/users -> 200 len=1381
https://demo-company.alfaview.com/api/v1/users` -> 200 len=1381

## 2026-09-03 18:48:54 UTC
https://apis.alfaview.com/v2/docs/openapi.json` -> HTTP 404
https://beta-apis.alfaview.com/v2/docs/openapi.json` -> HTTP 404
https://apis.alfaview.com/v2/languages` -> HTTP 404
https://beta-apis.alfaview.com/v2/languages` -> HTTP 404
https://beta-apis.alfaview.com/v2/docs/openapi.json -> 200 len=?
https://apis.alfaview.com/v2/docs/openapi.json -> 200 len=?
https://beta-apis.alfaview.com/v2/debug` -> HTTP 404
https://beta-apis.alfaview.com/v2/admin` -> HTTP 404
https://beta-apis.alfaview.com/v2/internal` -> HTTP 404
https://beta-apis.alfaview.com/v2/test` -> HTTP 404
https://beta-apis.alfaview.com/v2/health` -> HTTP 404
https://insider-webclient.alfaview.com/ -> 200 len=4396

## 2026-09-03 21:22:16 UTC
https://insider-webclient.alfaview.com/ -> 200 len=4396
https://insider-webclient.alfaview.com/api/* -> HTTP 404
https://beta-apis.alfaview.com/v2/languages -> HTTP 401
https://insider-webclient.alfaview.com/` -> HTTP 404
https://insider-webclient.alfaview.com/api` -> HTTP 404
https://insider-webclient.alfaview.com/admin` -> HTTP 404
https://insider-webclient.alfaview.com/debug` -> HTTP 404
https://insider-webclient.alfaview.com/internal` -> HTTP 404
https://insider-webclient.alfaview.com/health` -> HTTP 404
https://insider-webclient.alfaview.com/api/health -> HTTP 404
https://insider-webclient.alfaview.com/api/config -> HTTP 404

## 2026-09-03 23:25:03 UTC
https://alfacheck-audio.alfaview.com/` -> ERR The read operation timed out
https://alfacheck-engine.alfaview.com/` -> ERR The read operation timed out
https://alfacheck-video.alfaview.com/` -> ERR The read operation timed out

## 2026-09-04 01:14:01 UTC
https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/ -> 200 len=9
https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/` -> HTTP 400
https://alfacheck-engine.alfaview.com/ -> ERR The read operation timed out
https://alfacheck-engine.alfaview.com/health -> ERR The read operation timed out
https://alfacheck-engine.alfaview.com/status -> ERR The read operation timed out
https://alfacheck-audio.alfaview.com/ -> ERR The read operation timed out
https://alfacheck-audio.alfaview.com/media -> ERR The read operation timed out
https://alfacheck-audio.alfaview.com/recordings -> ERR The read operation timed out
https://alfacheck-video.alfaview.com/ -> ERR The read operation timed out

## 2026-09-04 06:01:02 UTC
https://apis.alfaview.com/v2/docs/openapi.json` -> HTTP 404
https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/ -> 200 len=9
https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/.well-known/openid-configuration -> 200 len=9
https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/health -> 200 len=9

## 2026-09-04 10:12:17 UTC
https://apis.alfaview.com/v2/auth/guest-link` -> HTTP 404

## 2026-09-04 14:25:13 UTC
https://apis.alfaview.com/v2/auth/guest-link` -> HTTP 404
https://alfatraining.alfaview.com/ -> 200 len=1381
https://bhc.alfaview.com/ -> 200 len=1381
https://kh-freiburg.alfaview.com/ -> 200 len=1381

## 2026-09-04 17:51:06 UTC
https://apis.alfaview.com/v2/auth/guest-link` -> HTTP 404

## 2026-09-04 20:04:50 UTC
https://beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com/ -> ERR <urlopen error timed out>
https://alfaview.com/ -> 200 len=?
https://sso.alfaview.com/ -> 200 len=0
https://beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com/` -> ERR <urlopen error timed out>

## 2026-09-04 22:10:15 UTC
https://alfaview.com/ -> 200 len=?
https://sso.alfaview.com/ -> 200 len=0
https://alfaview.com/@evil.com -> HTTP 404
https://beta-ionoscloud-21-beta-engine-gw4qw.alfaview.com/ -> ERR <urlopen error timed out>
https://alfaview.com/` -> HTTP 404
https://sso.alfaview.com/authorize?client_id=test&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` -> HTTP 404

## 2026-09-05 00:15:17 UTC
https://sso.alfaview.com/oauth2/authorize -> 200 len=0
https://sso.alfaview.com/oauth2/authorize?client_id=<valid_id>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test -> HTTP 400
https://sso.alfaview.com/oauth2/token -> HTTP 405
https://test.alfaview.com/ -> 200 len=494
https://app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js` -> 200 len=1381
https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` -> HTTP 400
https://sso.alfaview.com/oauth2/register` -> HTTP 404

## 2026-09-05 04:34:25 UTC
https://sso.alfaview.com/oauth2/authorize -> 200 len=0
https://app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js -> 200 len=1090319
https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123 -> HTTP 400
https://sso.alfaview.com/.well-known/openid-configuration -> 200 len=0
https://sso.alfaview.com/.well-known/jwks.json -> 200 len=0
https://test.alfaview.com/ -> 200 len=494
https://app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js` -> 200 len=1381
https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` -> HTTP 400
