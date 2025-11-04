# AWS Lambda Power Tuning

## Overview

During the process of migrating a legacy application to serverless infrastructure, AWS Lambda plays a key role. Deploying Lambda functions may sound simple, but there are important learnings during this process related to memory allocation and cost management that can significantly help applications perform with less latency and high performance while optimizing costs.

## What is AWS Lambda Power Tuning?

AWS Lambda Power Tuning is a state machine powered by AWS Step Functions that helps you optimize your Lambda functions for cost and/or performance in a data-driven way.

The state machine is designed to be easy to deploy and fast to execute. Also, it's language agnostic so you can optimize any Lambda functions in your account.

Basically, you can provide a Lambda function ARN as input and the state machine will invoke that function with multiple power configurations (from 128MB to 10GB, you decide which values). Then it will analyze all the execution logs and suggest you the best power configuration to minimize cost and/or maximize performance.

## Performance Analysis

The chart below compares the invocation time and invocation cost for different memory sizes:

![Lambda Power Tuning Results](images/lambda-power-tuning-results.png)

**Key Insights:**
- **Invocation Time (Red Line)**: Decreases as memory increases
  - 128 MB: ~2,800 ms
  - 256 MB: ~1,600 ms
  - 512 MB: ~900 ms
  - 1024 MB: ~600 ms (best performance)

- **Invocation Cost (Blue Line)**: Increases as memory increases
  - 128 MB: ~$0.000005 (best cost)
  - 256 MB: ~$0.000006
  - 512 MB: ~$0.000008
  - 1024 MB: ~$0.0000105

**Summary:**
- **Best Cost**: 128 MB
- **Best Time**: 1024 MB
- **Worst Cost**: 1024 MB
- **Worst Time**: 128 MB

This demonstrates the trade-off between performance and cost. Higher memory allocation leads to faster execution but increased costs, while lower memory results in slower execution but lower costs.

## Architecture

The sample application architecture demonstrates a typical serverless order processing workflow:

![Application Architecture](images/aws-architecture.png)

**Architecture Components:**
1. **API Gateway**: Receives `POST` requests to `/cart/checkout` endpoint
2. **Amazon SQS**: Queues incoming orders for asynchronous processing
3. **AWS Lambda**: Processes orders from the queue (this is where power tuning is critical)
4. **Amazon RDS**: Persists processed order data

**Workflow:**
- Client makes a POST request to API Gateway
- API Gateway forwards order details to Amazon SQS
- SQS queues the orders
- Lambda function is triggered to process orders
- Processed data is stored in Amazon RDS

This architecture highlights the importance of Lambda optimization, as it's a key component in the processing pipeline.

## How to Run a Load Test with Postman

### Prerequisites

- Download the latest Postman application for this testing

### Setup Instructions

1. **Create a New Collection**
   - Open Postman and create a new collection for your Lambda function testing
   - Add your API request (e.g., POST request to your Lambda endpoint)
   - Configure the request body with appropriate JSON payload

   ![Create Collection in Postman](images/postman-collection.png)

2. **Configure Performance Test**
   - Navigate to the **Runner** tab in Postman
   - Select the **Performance** tab (instead of Functional)
   - Configure your load profile:
     - **Load profile**: Fixed
     - **Virtual users**: Set the number of VUs (e.g., 20 VUs)
     - **Test duration**: Set the duration (e.g., 10 minutes)
   - Free tier supports up to 100 VU (Virtual Users)
   - Enterprise tier can support up to 500 VU for parallel runs

   ![Configure Performance Test in Postman](images/postman-runner-config.png)

3. **Run Performance Tests**
   - Click "Run Testcollection" to start the performance test
   - Monitor the test execution in real-time
   - After completion, view the detailed performance report

   ![Performance Test Results](images/postman-performance-results.png)

   **Key Metrics to Review:**
   - Total requests sent
   - Requests per second
   - Average response time
   - P90, P95, P99 response times
   - Error rate
   - Performance graphs showing trends over time

### Adjusting Lambda Memory Configuration

You can adjust the Lambda memory configuration based on your average response time requirement. It's recommended to rely on your QA team for these testings to ensure comprehensive analysis.

## Best Practices

- **Memory Allocation**: Start with lower memory configurations and gradually increase based on performance requirements
- **Cost Optimization**: Balance between memory allocation and execution time to minimize overall cost
- **Performance Testing**: Use load testing tools like Postman to validate performance under various memory configurations
- **Monitoring**: Continuously monitor Lambda function metrics to identify optimization opportunities

## Resources

- [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/)
- [AWS Step Functions Documentation](https://docs.aws.amazon.com/step-functions/)
- [Postman Documentation](https://learning.postman.com/docs/)

## Contributing

Feel free to submit issues and enhancement requests!

## License

This project is open source and available for use.

