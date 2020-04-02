# Pact_From_Scratch_StepByStep (For Beginners)

Pact is a contract testing framework where we can define consumer expectations for a given provider 
(that can be an HTTP REST service). Contract testing on a broader level can be thought of testing
the communication layers between the services. 
There are two main type of actors in contract testing:-
1. Providers
2. Consumers

Provider is an application responsible for publishing an API;
Consumer is another application which uses the API;

There are 2 types of contract testing:
1. Provider Driven (Provider defines the contract and consumer verifies)
2. Consumer Driven (Consumer defines expectations and provider checks)

In this demo, I have used Consumer driven contract testing.
It is a way to formalize these expectations into a contract between consumer-provider pair. 
Once contract is established, contract testing ensured automatically that contract doesn't break unexpectedly.
If API changes break anything, it also allows provider to know which of their consumers are affected and when 
it is safe to push the changes, if any.

So, now lets start with step-by-step demo.

# Let's Get Started

Lets start by making our own API. It is just few steps away
In terminal type below commands:

1. Install JSON Server
  
        npm install -g json-server

2. Create a db.json file with some data

        vim db.json
        
 Add your json below:
 
       {
        "posts": [
          { "id": 1, "title": "json-server", "author": "typicode" }
        ]
      }
      
      Press esc key and then :wq to save and exit.
      
3.  Start json server

        json-server --watch db.json
        
 4. Now if you go to http://localhost:3000/posts/1, you'll get

        { "id": 1, "title": "json-server", "author": "typicode" }

Our API is created successfully. Now create a maven project in eclipse. Follow below steps:

  1. Create maven project "pactConsumer".
  
  2. In pom.xml, add below dependencies and plugins:
  
      parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.0.RELEASE</version>
    </parent>
 	  <dependencies> 
        <dependency>
            <groupId>au.com.dius</groupId>
            <artifactId>pact-jvm-consumer-junit_2.11</artifactId>
            <version>3.5.0</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>au.com.dius</groupId>
            <artifactId>pact-jvm-provider-junit_2.11</artifactId>
            <version>3.5.0</version>
            <scope>test</scope>
        </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-web</artifactId>
                <version>2.1.0.RELEASE</version>
            </dependency>

        </dependencies>
      <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <configuration>
                <systemPropertyVariables>
                    <pact.rootDir>target/pacts</pact.rootDir>
                </systemPropertyVariables>
            </configuration>
            </plugin>
            <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
       </plugins>
       </build>
   
 3. Create class "TestProvider" in package src.test.java
 
    This class is divided into 3 sections:
    
    In first section, we create a @rule. So here we pass the provider name as below:
    
        @Rule
        public PactProviderRuleMk2 provider = new PactProviderRuleMk2("test_provider", this);
 
    In second section, we will define a contract. In this demo, I have defined contract for only one HTTP method.
    So, I have a GET method request that returns json with 3 fields id, title and author. When request succeeds it returns
    200 HTTP response code. To define such a contract, we use @Pact annotation & we pass consumer name for which pact is 
    defined.
     
        @Pact(consumer = "test_consumer")
        public RequestResponsePact createPact(PactDslWithProvider builder) {
          Map<String, String> headers = new HashMap<>();
            headers.put("Content-Type", "application/json");

            return builder
                .given("test GET")
                    .uponReceiving("GET REQUEST")
                    .path("/posts/1")
                    .method("GET")
                   .willRespondWith()
                    .status(200)
                    .body("{\n" + 
                        "  \"id\": 1,\n" + 
                        "  \"title\": \"json-server\",\n" + 
                        "  \"author\": \"typicode\"\n" + 
                        "}")
                .toPact();
          }

    Using PactDSL we define that for given GET request we want to return 200 response code with specific headers and body.
    
    Now, that we have our contract, in third section we have to create test against it.
    We need @PactVerification annotation. It takes care of the http service.
    In this, we only need to send the GET request & assert that the response complies with the contract.
    
          @Test
          @PactVerification()
          public void givenGet_whenSendRequest_shouldReturn200WithProperHeaderAndBody() {

              // when
              ResponseEntity<String> response = new RestTemplate()
                .getForEntity(provider.getUrl() + "/posts/1", String.class);

              // then
              assertEquals(200, response.getStatusCode().value());
          }
