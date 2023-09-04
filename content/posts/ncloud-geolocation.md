  ---
author: "yeorinhieut"
title: "네이버 클라우드 Geolocation Api"
date: "2023-09-04"
description: "ncloud geolocation api"
tags: ["snippet"]
ShowToc: false
ShowBreadCrumbs: false
---

```
import axios from 'axios';
import CryptoJS from 'crypto-js';

export default class GeolocationService {
    private readonly baseUrl: string = 'https://geolocation.apigw.ntruss.com/geolocation/v2/geoLocation';
    private readonly accessKey: string = process.env.GEOLOCATION_ACCESS_KEY as string;
    private readonly secretKey: string = process.env.GEOLOCATION_SECRET_KEY as string; 

    private makeSignature(method: string, baseString: string, timestamp: string, accessKey: string): string {

        const hmac = CryptoJS.algo.HMAC.create(CryptoJS.algo.SHA256, this.secretKey);
        hmac.update(method + ' ' + baseString + '\n' + timestamp + '\n' + accessKey);

        return hmac.finalize().toString(CryptoJS.enc.Base64);
    }

    async getLocation(ip: string) {
        const timestamp = Date.now().toString();
        const method = 'GET';
        const queryString = `ip=${ip}&ext=t&responseFormatType=json`;
        const baseString = `/geolocation/v2/geoLocation?${queryString}`;
        const signature = this.makeSignature(method, baseString, timestamp, this.accessKey);

        const headers = {
            'x-ncp-apigw-timestamp': timestamp,
            'x-ncp-iam-access-key': this.accessKey,
            'x-ncp-apigw-signature-v2': signature,
        };

        try {
            const url = `${this.baseUrl}?${queryString}`;
            const response = await axios.get(url, { headers });
            
            return response.data;
        } catch (error) {
            throw error;
        }
    }
}
```
