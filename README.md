AWS customers who provide software solutions that integrate with AWS often require design patterns that offer some flexibility. They must build, support, and expand products and solutions to meet their end user business requirements. These design patterns must use the underlying services and infrastructure through API operations. As we will show, third-party solutions can integrate with Amazon Connect to initiate customer-specific workflows. You don’t need specific utterances when using Amazon Lex to convert speech to text.

Introduction to Amazon Connect workflows
Platform as a service (PaaS) systems are built to handle a variety of use cases with various inputs. These inputs are provided by upstream systems and sometimes result in complex integrations. For example, when creating a call center management solution, these workflows may require opaque transcription data to be sent to downstream third-party system. This pattern allows the caller to interact with a third-party system with an Amazon Connect flow. The solution allows for communication to occur multiple times. Opaque transcription data is transferred between the Amazon Connect contact flow, through Amazon Lex, and then to the third-party system. The third-party system can modify and update workflows without affecting the Amazon Connect or Amazon Lex systems.

API workflow use case
AnyCompany Tech (our hypothetical company) is a PaaS company that allows other companies to quickly build workflows with their tools. It provides the ability to take customer calls with a request-response style interaction. AnyCompany built an API into the Amazon Connect flow to allow their end users to return various response types. Examples of the response types are “disconnect,” “speak,” and “failed.”

Using Amazon Connect, AnyCompany allows their end users to build complex workflows using a basic question-response API. Each customer of AnyCompany can build workflows that respond to a voice input. It is processed via the high-quality speech recognition and natural language understanding capabilities of Amazon Lex. The caller is prompted by “What is your question?” Amazon Lex processes the audio input then invokes a Lambda function that connects to AnyCompany Tech. They in turn initiate their customer’s unique workflow. The customer’s workflow may change over time without requiring any further effort from AnyCompany Tech.

Questions graph database use case
AnyCompany Storage is a company that has a graph database that stores documents and information correlating the business to its inventory, sales, marketing, and employees. Accessing this database will be done via a question-response API. For example, such questions might be: “What are our third quarter earnings?”, “Do we have product X in stock?”, or “When was Jane Doe hired?” The company wants the ability to have their employees call in and after proper authentication, ask any question of the system and receive a response. Using the architecture in Figure 1, the company can link their Amazon Connect implementation up to this API. The output from Amazon Lex is passed into the API, a response is received, and it is then passed to Amazon Connect.

Amazon Connect third-party system architecture
Figure 1. End-customer call flow
Figure 1. End-customer call flow

User calls Amazon Connect using the telephone number for the connect instance.
Amazon Connect receives the incoming call and starts an Amazon Connect contact flow. This Amazon Connect flow captures the caller’s utterance and forwards it to Amazon Lex.
Amazon Lex starts the requested bot. The Amazon Lex bot translates the caller’s utterance into text and sends it to AWS Lambda via an event.
AWS Lambda accepts the incoming data, transforms or enhances it as needed, and calls out to the external API via some transport
The external API processes the content sent to it from AWS Lambda.
The external API returns a response back to AWS Lambda.
AWS Lambda accepts the response from the external API, then forwards this response to Amazon Lex.
Amazon Lex returns the response content to Amazon Connect.
Amazon Connect processes the response.
Solution components
Amazon Connect
Amazon Connect allows customer calls to get information from the third-party system. An Amazon Connect contact flow is required to get the callers input, which is then sent to Amazon Lex. The Amazon Lex bot must be granted permission to interact with an Amazon Connect contact flow. As shown in Figure 2, the Get customer input block must call the fallback intent of the Amazon Lex bot. Figure 2 demonstrates a basic flow used to get input, check the response, and perform an action.

Figure 2. Basic Amazon Connect contact flow to integrate with Amazon Lex
Figure 2. Basic Amazon Connect contact flow to integrate with Amazon Lex

Amazon Lex
Amazon Lex converts the voice given by a caller into text, which is then processed by a Lambda function. When setting up the Amazon Lex bot you will require one fallback intent (Figure 3), one unused intent (Figure 4), and one clarification prompts disabled (Figure 5).

Figure 3. Amazon Lex fallback intent
Figure 3. Amazon Lex fallback intent

The fallback intent is created by using the pre-defined AMAZON.FallbackIntent and must be set up to call a Lambda function in the Fulfillment section. Using the fallback intent and Lambda fulfillment causes the system to ignore any utterance pattern and pass any translation directly to the Lambda function.

Figure 4. Amazon Lex unused intent
Figure 4. Amazon Lex unused intent

The unused intent is only created to satisfy Amazon Lex’s requirement for a bot to have at least one custom intent with a valid utterance.

Figure 5. Amazon Lex error handling clarification prompts
Figure 5. Amazon Lex error handling clarification prompts

In the error handling section of the Amazon Lex bot, the clarification prompts must be disabled. Disabling the clarification prompts stops the Amazon Lex bot from asking the caller to clarify the input.

AWS Lambda
AWS Lambda is called by Amazon Lex, which is used to interact with the third-party system. The third-party system will return values, which the Lambda will add to its session attributes resulting object. The resulting object from AWS Lambda requires the dialog action to be set up with the type set to “Close,” fulfillmentState set to “Fulfilled,” and the message contentType set to “CustomPayload”. This will allow Amazon Lex to pass the values to Amazon Connect without speaking the results to the caller.

Conclusion
In this blog we showed how Amazon Connect, Amazon Lex, and AWS Lambda functions can be used together to create common interactions with third-party systems. This is a flexible architecture and requires few changes to the Amazon Connect contact flow. You don’t have to set up pre-defined utterances that limit the allowed inputs. Using this solution, AWS customers can provide flexible solutions that interact with third-party systems.
