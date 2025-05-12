Well, it's one of those moments again where I can't figure out how to explain the thing I'm trying to do. What a crazy world. I originally set out to make an app that let me keep track of my kitchen, and instead, it has blown out into a full-blown inspection of Foodstuffs' API for both Pak'nSave and New World.


# Step One - Website Madness
Like any good story, we begin at the start—or is it the beginning? Oh well, who cares.

I started our by bringing up the [Pak'nSave](https://www.paknsave.co.nz/) website, thinking that would be a decent place to start doing what I want. After all, it's half of the service I'm wanting from my app—the grocery side.
Everything seemed normal to start with, I saw some [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)'s go out which looked hopeful, oh how naive I was. 

![Screenshot 2025-05-09](https://github.com/user-attachments/assets/dcccb288-a09a-467d-8017-29201489a75c)

Let’s break that down a little more. We’ll start with the key one I was most interested in: AutoComplete. This fired when I searched for an item in the shop’s search bar. Well, what do you know about that?

![Recording-2025-05-09-221115](https://github.com/user-attachments/assets/f28d109d-ed79-42d5-b441-28e36be60642)

Simple, right? Well, not quite—you surely noticed the pessimistic tone. Upon looking at the Request Headers, I can see some pretty funky looking cookies that don't look exactly nice to try recreate, not exactly hard, it's just more what sort of sick individual puts JSON in a cookie?

![Screenshot 2025-05-09 221822](https://github.com/user-attachments/assets/5977c843-1953-474e-b947-bff7a55940e7)

C’mon Foodstuffs, really? What’s with this? You’ve got the data inside `browser_nearest_store` duplicated across three other keys. Oh, and now that you’ve got me started, there’s also `Region` what’s that about? Can’t you get the region from the StoreID in any number of the cookies already storing that? I guess I can get over their messy cookie situation, and get on with it and start working out the steps required to forge a request. But I guess before I do that, I still haven't shown you how they send the search term, this is done sensibly, or about as close to it as I've learnt to expect as I can expect from our biggest supermarket chain. Looking at the Request Payload, I see;

```json
{"SearchTerm":"Mil","IsMobileView":false}
```

Well hey, how about that, it's my search term, only now is it all coming together. Perfect. So let's fire up a PowerShell and use our old friend `Invoke-WebRequest` or `Invoke-RestMethod` so we can start drafting up a rough plan of how we're going to interact with their API, afterall there is no documentation so I guess I was in for a lot of pain. Best of all, I didn't know how bad it could be until it got bad.

Oh, surely that's because I used `WebRequest`, not `RestMethod`, surely it's because there is something done under the hood of these calls to work.
![image](https://github.com/user-attachments/assets/66f1962c-fe07-4b4c-b2d7-a89c57410b26)

Oh, oh no. I know that `Just a moment...`, I'll give you a second, you may not have seen it recently with how good its gotten, but here's the answer, it's Cloudflares DDOS/Bot detection filter. On their API call? That seems a bit, I don't know, what's the word? Odd? Well, I guess we'll try cURL using Firefox to get the command.
![image](https://github.com/user-attachments/assets/9576c57f-6432-4654-acb5-3b7ac815de14)

Well, that's a long line of nothings, I don't quite know how I feel about having to construct this anytime I want to interact with their website, I see some Cloudflare related cookies in there, and if the protection has been made to stop bots, I think I might be in for a rough ride of it if I am going to try... But, before we give up hope there is another way that you might have already thought of before I even started this.
```
curl "https://www.paknsave.co.nz/CommonApi/SearchAutoComplete/AutoComplete" --compressed -X POST -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:138.0) Gecko/20100101 Firefox/138.0" -H "Accept: application/json, text/plain, */*" -H "Accept-Language: en-US,en;q=0.5" -H "Accept-Encoding: gzip, deflate, br, zstd" -H "Referer: https://www.paknsave.co.nz/" -H "Content-Type: application/json;charset=utf-8" -H "__RequestVerificationToken: mtDOI3nd3pqVe8dyboB5DrHBOQpgNi0zyKodLHmWlC2ZjOjXgCxX_Fp7ymF4e5bA_amHkLPjg7IZ6RLbh97nO5Y_6ME1" -H "Origin: https://www.paknsave.co.nz" -H "DNT: 1" -H "Connection: keep-alive" -H "Cookie: SessionCookieIdV3=178f9b65b5ea42698648cf4ce5c210d6; GeolocationInformation={""IsUnknown"":false,""AreaCode"":""N/A"",""BusinessName"":""N/A"",""City"":""Hamilton"",""Country"":""NZ"",""Dns"":""N/A"",""Isp"":""N/A"",""Latitude"":-38.867,""Longitude"":175.7863,""MetroCode"":""N/A"",""PostalCode"":""3200"",""Region"":""N/A"",""Url"":""N/A""}; eCom_STORE_ID=8cd700ae-d96f-4761-bd7a-805d6b93536d; server_nearest_store_v2={""StoreId"":""076e8177-943b-41fc-a885-ba3d28297acf"",""UserLat"":""-37.6649"",""UserLng"":""175.3045"",""StoreLat"":""-37.7799"",""StoreLng"":""175.27281"",""IsSuccess"":true}; eCom_StoreId_NotSameAs_Brands=false; STORE_ID_V2=8cd700ae-d96f-4761-bd7a-805d6b93536d^|False; Region=SI; AllowRestrictedItems=true; cf_clearance=TpOINXiTG8w7_fHpLjy2v5AEalg0AtogHvXoJ9Hloss-1746829253-1.2.1.1-fkfd8z0ZiWJhInT.qSxidsLXVC3p6dFtZWjtQBSFvNUCNdWeEI1TeZAF3qDvEgOU6zmvc74eX54wPNxJxJCAmShmh.ea1B3rG7o3ikPfHyu.a2eUNeyyyoEXTlgmkgZHyjEy4g8tUPf2R5J5FygSjiVbv6SGJkxPw.MgboBzmOLU2nNDI6dePfs3FxZrs80VeMKbWnVA4szhKoJk3vbE_wll0Pep.SLwd6rAX98Q5RVCmMfT5eTh3eAB9V0CaGK8gSbRf4Ni.f5vCa9Em09rtK01tuAn1Rs8H.Q92kKUWb9a7T0K.gk1hA.pzUCbZ.fjJzyGYs0e0tlTdwW6Vib57YjECb.YePfJGwJbnWhPJg0; brands_server_nearest_store={""StoreId"":""{9B8BDE0F-FBD1-4BF4-B969-6DFD3C206E4A}"",""UserLat"":""-37.7799363153992"",""UserLng"":""175.272786211917"",""StoreLat"":""-37.7799363153992"",""StoreLng"":""175.272786211917"",""IsSuccess"":true}; brands_store_id={2FBD858F-6311-417E-9665-97F273D2FAC6}; region_code=SI; eComm_Coordinate_Cookie={""latitude"":-43.4853687809161,""longitude"":172.61498059995094}; browser_nearest_store={^%^22IsSuccess^%^22:true^%^2C^%^22StoreId^%^22:^%^22b92cc33f-b5a8-4b57-9b82-412946800020^%^22^%^2C^%^22UserLat^%^22:^%^22-38.867^%^22^%^2C^%^22UserLng^%^22:^%^22175.7863^%^22^%^2C^%^22StoreLat^%^22:^%^22-38.685532^%^22^%^2C^%^22StoreLng^%^22:^%^22176.073247^%^22}; browser_nearest_store_v2={^%^22IsSuccess^%^22:true^%^2C^%^22StoreId^%^22:^%^22b92cc33f-b5a8-4b57-9b82-412946800020^%^22^%^2C^%^22UserLat^%^22:^%^22-38.867^%^22^%^2C^%^22UserLng^%^22:^%^22175.7863^%^22^%^2C^%^22StoreLat^%^22:^%^22-38.685532^%^22^%^2C^%^22StoreLng^%^22:^%^22176.073247^%^22}; brands_store_reset=; shell^#lang=en; __RequestVerificationToken=2Whsj1zZxvLeDxTSZjJYypdkLqz-qDYttgv1bcRZqbZCCyBItv6odE6xOFiyHIn80oTignmpK9FLk2sFRTIMrOGTPL81; sxa_site=PAKnSAVE; fs-store-select-tooltip-closed=true; __cfruid=81b42a33f5054f32ef5d043f7d896c8bee14662b-1746344753; ASP.NET_SessionId=cwqybmjcpttulospfddqa2gb; __cf_bm=E5HDLU1sPHo8pCXjOPMU0vG9RCe04Ei0Zp0PsOuJ1l4-1746829252-1.0.1.1-Vljh78jJ_Ql6ylRaVUoMXv.Qq.vJYVpS91GEhWleL1CILUJvlVBxYskEz6SsbseAMw8ctnaVSY06cQvu1P5xR5RY.80g_nhzOChlMyGnMlc_4_skLh84NGxzTpHCr8nn" -H "Sec-Fetch-Dest: empty" -H "Sec-Fetch-Mode: cors" -H "Sec-Fetch-Site: same-origin" -H "TE: trailers" --data-raw "{""SearchTerm"":""Mil"",""IsMobileView"":false}"
```

![image](https://github.com/user-attachments/assets/635f46b3-5aa4-4680-89ce-548c34f7df31)

Oh, good. cURL doesn't support the compression, that's okay, we can work around it again, this is just a way for websites to save bandwidth, it shouldn't matter all that much if we remove `br` and `zstd`;

![image](https://github.com/user-attachments/assets/3a4b8f05-88d8-4a01-80c3-d6016772f60d)

Would you look at that, I got a reply back, unbelievably they sell no `Mil` items... Huh? Firefox, shows me they do and so does that Gif above (It's jif by the way)

![image](https://github.com/user-attachments/assets/325ec12a-b896-45b7-9026-e7035c01b721)

Ok there is a lot to break down here, so let's just take a step back and think, we've copied the request directly from Firefox, into a cURL and.... nothing. I got errors when using naitive PowerShell methods, which again, doesn't make this project look hopeful... [This is where the real game begins](https://www.youtube.com/@MittenSquad/videos).


Well, what's one to do now? I guess I just keep going, I go about booting up WSL to use a Linux build of cURL thinking that it might be a Windows issue, sadly I have to report I got a Cloudflare prompt, so I guess I'm back to square one. 

## Step Two - Mobile Boogaloo
This is the podracing section, where the story starts to get weird, and we certainly end up in some real odd places from where we started.

I'll save the madness I went through, but the long story short involves installing GAPPs on an old build of Android because I needed root in order to bypass the certificate validation the Pak 'n Save app uses, if there is a better way around it I'm happy to listen.
![Screenshot 2025-05-11 145112](https://github.com/user-attachments/assets/d1bfb543-f593-4a9f-8ef9-bcd8c70873fb)
But, we have Http Toolkit looking at traffic from our app, there is a lot of Google noise, but one domain stands out then all the rest, `fsniwaikato.kiwi` what the heck is that? You should do a DNS lookup on that, there are some weird sub-doimains on it. They might be worth their own investigation. But onwards, we can start by filtering our requests to that to help display the stuff we're actually interested in.

![image](https://github.com/user-attachments/assets/53b193e9-d9d2-4a55-ab86-5b564da177cf)
Well that looks a little bit more readable and easy to follow

 <a href="https://arefu.github.io/Foodstuffs-Madness-Part-Two">More?</a>
