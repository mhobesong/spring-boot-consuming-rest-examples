# spring-boot-consuming-rest-examples

Spring Boot Web Controller example code for consuming third party APIs, getting respnose messages and respnose codes.
Also contains examples on how to handle error respnoses.

```java
package com.examples.boilerplates;

import java.util.HashMap;

import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.HttpClientErrorException;
import org.springframework.web.client.RestTemplate;

@RestController
class TestController {

	/**
	 * Reading GET request query params
	 * For example: http://localhost:8080/getparams?foo=bar&bar=foo
	 * will return: The value of 'foo' is 'me' and the value of 'bar' is 'you'
	 */
	@GetMapping("/getparams")
	public ResponseEntity<String> getparams(
			@RequestParam(name="foo",required=true,defaultValue="helloWorld") String param1,
			@RequestParam(name="bar",required=true,defaultValue="helloWorld") String param2
			){
		String response = "The value of 'foo' is '" +param1+ "' and the value of 'bar' is '" +param2+ "'";
		return new ResponseEntity<String>(response, new HttpHeaders(), HttpStatus.OK);
	}
	
	/**
	 * Consuming a GET API
	 * Example: Get current EST time from "http://worldclockapi.com/api/json/est/now"
	 */
	@GetMapping("/getapi")
	public ResponseEntity<HashMap> getapi(){
		String url = "http://worldclockapi.com/api/json/est/now";
		RestTemplate restTemplate = new RestTemplate();
		HttpHeaders headers = new HttpHeaders();
		HashMap<String, String> params = new HashMap<String, String>();
		HttpEntity<HashMap> entity = new HttpEntity(params, headers);
		ResponseEntity<HashMap> response = restTemplate.exchange(url, HttpMethod.GET, entity, HashMap.class);
		return response;
	}
	
	/**
	 * Get status code of a rest request response
	 * Example: Get current EST time from "http://worldclockapi.com/api/json/est/now"
	 * Returns: "200"
	 */
	@GetMapping("/getstatuscode")
	public int getstatuscode(){
		String url = "http://worldclockapi.com/api/json/est/now";
		RestTemplate restTemplate = new RestTemplate();
		HttpHeaders headers = new HttpHeaders();
		HashMap<String, String> params = new HashMap<String, String>();
		HttpEntity<HashMap> entity = new HttpEntity(params, headers);
		ResponseEntity<HashMap> response = restTemplate.exchange(url, HttpMethod.GET, entity, HashMap.class);
		return response.getStatusCode().value();
	}
	
	/**
	 * Get error response code when consuming rest apis
	 * Returns: "404"
	 */
	@GetMapping("/handleerrorresponse")
	public int handleerrorresponse(){
		String url = "http://worldclockapi.com/api/json/es";
		RestTemplate restTemplate = new RestTemplate();
		HttpHeaders headers = new HttpHeaders();
		HashMap<String, String> params = new HashMap<String, String>();
		HttpEntity<HashMap> entity = new HttpEntity(params, headers);
		
		try {
		ResponseEntity<HashMap> response = restTemplate.exchange(url, HttpMethod.GET, entity, HashMap.class);
		} catch(HttpClientErrorException ex) {
			return ex.getRawStatusCode();
		}
		return 0;
	}
	
	/**
	 * Get error response message when consuming rest apis
	 * Returns: "{"$id":"1","message":"No HTTP resource was found that matches the request URI 'http://worldclockapi.com/api/json/es'."}"
	 */
	@GetMapping("/handleerrorresponse2")
	public ResponseEntity<String> handleerrorresponse2(){
		String url = "http://worldclockapi.com/api/json/es";
		RestTemplate restTemplate = new RestTemplate();
		HttpHeaders headers = new HttpHeaders();
		HashMap<String, String> params = new HashMap<String, String>();
		HttpEntity<HashMap> entity = new HttpEntity(params, headers);
		
		try {
			ResponseEntity<String> response = restTemplate.exchange(url, HttpMethod.GET, entity, String.class);
		} catch(HttpClientErrorException ex) {
			String message = ex.getResponseBodyAsString();
			return new ResponseEntity<String>(message, headers, ex.getStatusCode());
		}
		return null;
	}
}
```
