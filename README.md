# Paytm-integration


<p>Our Backend Server code for test Purpose 'Paytm.js`</p>

```
const https = require('https');
var PaytmChecksum = require("paytmchecksum");
CALL_BACK_URL :"https://securegw-stage.paytm.in/theia/paytmCallback?ORDER_ID="
WEBSTAGING :"WEBSTAGING"

var paytmParams = {};
const mid = "merchant_mid"



paytmParams["MID"] = mid;
paytmParams["INDUSTRY_TYPE_ID"]="Retail";
paytmParams["CHANNEL_ID"]="WAP";
paytmParams["WEBSITE"] = "WEBSTAGING";


const paytm = async(model,result)=> {
    var finalResponse={status:0,data:null};
    paytmParams.body = {
        "requestType"   : "Payment",
        "mid"           : mid,
        "websiteName"   : "WEBSTAGING",
        "orderId"       : "ORDERID_9858765",
         "callbackUrl"   : "http://localhost:3000/response",
        "txnAmount"     : {
            "value"     : "3500",
            "currency"  : "INR",
        },
        "userInfo"      : {
            "custId"    : "465454",
        },
    };

    PaytmChecksum.generateSignature(JSON.stringify(paytmParams.body),"merchant_key").then(function(checksum){
        paytmParams.head = {
        "signature"    : checksum
    };
    var post_data = JSON.stringify(paytmParams);

    var options = {

        /* for Staging */
        hostname: 'securegw-stage.paytm.in',

        /* for Production */
        // hostname: 'securegw.paytm.in',

        port: 443,
        path: '/theia/api/v1/initiateTransaction?mid='+mid+'&orderId=ORDERID_9858765',
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
            'Content-Length': post_data.length
        }
    };

    var response = "";
    var post_req = https.request(options, function(post_res) {
        post_res.on('data', function(chunk){
            response += chunk
        });
        post_res.on('end', function(){
            console.log('Response: ', response);
        });
});
console.log("post_data",post_data);
post_req.write(post_data);
post_req.end();

});
return;
}

module.exports = paytm;

```

<p> On Request Data Send or Post to url  </p>

```
{
"MID":"merchant_mid_here","INDUSTRY_TYPE_ID":"Retail","CHANNEL_ID":"WAP","WEBSITE":"WEBSTAGING",
"body":
      {
      "requestType":"Payment","mid":"merchant_mid_here","websiteName":"WEBSTAGING","orderId":"ORDERID_9858765",
      "txnAmount":   {
                        "value":"3500","currency":"INR"
                      },
      "userInfo":  {
                      "custId":"465454"
                    }
      },

"head":  {
          "signature":"7u+O+8PxJqSm2bz6dny3dCqU3rS00mvAHMM1NMjcNFQXlOOLQBtyEwiVmhSXC/zyhxT3UG1wSp4hek9dIQk/LK5WJ3JwczVSwVVt+ChKMUs="
          }
}

    
```

<P>Response recieve </P>

```
Response:  {
              "head":{
                      "requestId":null,"responseTimestamp":"1720766679927","version":"v1"
                      },
              "body":{
                      "extraParamsMap":null,
                    "resultInfo":  {
                                    "resultStatus":"F","resultCode":"501","resultMsg":"System Error"
                                    }
                      }
          }

```
