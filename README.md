# How-to-use-Java-to-call-GraphQL-interface

关于前端如何调用GraphQL接口是很简单的事情，这个网上有大量的教程和文档。

但是很奇怪，通过后端Java来调用GraphQL接口却没有任何教程，可能是这种使用场景很少吧。


研究了下，总结了两种方法：

## 方法一：

      private Object getAsset(String token, String tenantId, String apikey) {

          HttpHeaders headers = new HttpHeaders();
          headers.add("Authorization", "Bearer "+ token);
          headers.add("customerId", tenantId);
          headers.add("apikey", apikey);
          headers.add("content-type", "application/json");

          String query = "{\"query\":\"query($assetType: String!){\\n  assets(assetFilter:{type: $assetType}){\\n    id\\n    label\\n    description\\n    properties {\\n      name\\n      value\\n    }\\n  }\\n}\",\"variables\":{\"assetType\":\"param\"}}";
          ResponseEntity<String> response = this.restTemplate.postForEntity(XXXURL,  new HttpEntity<>(query, headers), String.class);

          String bodyString = response.getBody();
          HttpStatus statusCode = response.getStatusCode();
          ObjectMapper om = new ObjectMapper();

          try{
            AssetResponse assetResponse = om.readValue(bodyString, AssetResponse.class);
            if(statusCode.is2xxSuccessful()){
              return assetResponse.getData().getAssets() ;
            } else {
              logger.error("call url {} err:{}", XXXURL, response.getStatusCode());
            }
          }catch(Exception e){
            logger.error("call url {} err:{}", XXXURL, response.getStatusCode());
          }

          return new HashMap<>();

        }
        
        
## 方法二
        
           private Object getAsset(String token, String tenantId, String apikey) 

      try{
         OkHttpClient client = new OkHttpClient().newBuilder()
               .build();
         MediaType mediaType = MediaType.parse("application/json");
         RequestBody body = RequestBody.create(mediaType, "{\"query\":\"query($assetType: String!){\\n  assets(assetFilter:{type: $assetType}){\\n    id\\n    label\\n    description\\n    properties {\\n      name\\n      value\\n    }\\n  }\\n}\",\"variables\":{\"assetType\":\"param\"}}");
         Request request = new Request.Builder()
               .url(XXXURL)
               .method("POST", body)
               .addHeader("customerId", "id01293e19b08e447b9bb36362f20836fa")
               .addHeader("Authorization", "Bearer "+token)
               .addHeader("apikey", "PtMG93DtsW7IIFzDJyapDanvgSJ1k7fVRrHdH1Z2")
               .addHeader("Content-Type", "application/json")
               .build();
         Response response = client.newCall(request).execute();
         String bodyString = response.body().string();
         int statusCode = response.code();
         ObjectMapper om = new ObjectMapper();
         try{
            // 省略对statusCode的判断
            AssetResponse assetResponse = om.readValue(bodyString, AssetResponse.class);
               return assetResponse.getData().getAssets() ;

         }catch(Exception e){
            logger.error("XXXXXXX");
         }

      }catch(Exception e){
         logger.error("XXXXXXX");
      }

      return new HashMap<>();
   }
