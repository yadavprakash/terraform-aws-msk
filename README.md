# Terraform-aws-msk

# Terraform AWS Cloud MSK Module

## Table of Contents
- [Introduction](#introduction)
- [Usage](#usage)
- [Examples](#examples)
- [License](#license)
- [Author](#Author)
- [Inputs](#inputs)
- [Outputs](#outputs)

## Introduction
This Terraform module creates an AWS Managed Streaming for Apache Kafka (Amazon MSK) along with additional configuration options.
## Usage
To use this module, you should have Terraform installed and configured for AWS. This module provides the necessary Terraform configuration for creating AWS resources, and you can customize the inputs as needed. Below is an example of how to use this module:

# Examples
```hcl
module "kafka" {
  source              = "git::https://github.com/yadavprakash/terraform-aws-msk?ref=v1.0.0"

  name                = local.name
  environment         = local.environment
  kafka_version       = "3.5.1"
  kafka_broker_number = 3

  broker_node_client_subnets  = module.subnets.private_subnet_id
  broker_node_ebs_volume_size = 20
  broker_node_instance_type   = "kafka.t3.small"
  broker_node_security_groups = [module.ssh.security_group_id, module.http_https.security_group_id]

  encryption_in_transit_client_broker = "TLS"
  encryption_in_transit_in_cluster    = true

  configuration_server_properties = {
    "auto.create.topics.enable" = true
    "delete.topic.enable"       = true
  }

  jmx_exporter_enabled    = true
  node_exporter_enabled   = true
  cloudwatch_logs_enabled = true
  s3_logs_enabled         = true
  s3_logs_bucket          = module.s3_bucket.id
  s3_logs_prefix          = "logs/msk-"

  scaling_max_capacity = 512
  scaling_target_value = 80

  client_authentication_sasl_scram         = false
  create_scram_secret_association          = false
  scram_secret_association_secret_arn_list = [module.secrets_manager.secret_arns]

  schema_registries = {
    team_a = {
      name        = "team_a"
      description = "Schema registry for Team A"
    }
    team_b = {
      name        = "team_b"
      description = "Schema registry for Team B"
    }
  }
  schemas = {
    team_a_tweets = {
      schema_registry_name = "team_a"
      schema_name          = "tweets"
      description          = "Schema that contains all the tweets"
      compatibility        = "FORWARD"
      schema_definition    = "{\"type\": \"record\", \"name\": \"r1\", \"fields\": [ {\"name\": \"f1\", \"type\": \"int\"}, {\"name\": \"f2\", \"type\": \"string\"} ]}"
    }
  }
}
```



## Examples
For detailed examples on how to use this module, please refer to the [examples](https://github.com/yadavprakash/terraform-aws-msk/tree/master/_example) directory within this repository.

## License
This Terraform module is provided under the **MIT** License. Please see the [LICENSE](https://github.com/yadavprakash/terraform-aws-msk/blob/master/LICENSE) file for more details.

## Author
Your Name
Replace **MIT** and **yadavprakash** with the appropriate license and your information. Feel free to expand this README with additional details or usage instructions as needed for your specific use case.

<!-- BEGIN_TF_DOCS -->
## Requirements

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 1.6.6 |
| <a name="requirement_aws"></a> [aws](#requirement\_aws) | >= 5.31.0 |

## Providers

| Name | Version |
|------|---------|
| <a name="provider_aws"></a> [aws](#provider\_aws) | >= 5.31.0 |

## Modules

| Name | Source | Version |
|------|--------|---------|
| <a name="module_labels"></a> [labels](#module\_labels) | git::https://github.com/yadavprakash/terraform-aws-labels.git | v1.0.0 |

## Resources

| Name | Type |
|------|------|
| [aws_appautoscaling_policy.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/appautoscaling_policy) | resource |
| [aws_appautoscaling_target.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/appautoscaling_target) | resource |
| [aws_cloudwatch_log_group.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/cloudwatch_log_group) | resource |
| [aws_glue_registry.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/glue_registry) | resource |
| [aws_glue_schema.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/glue_schema) | resource |
| [aws_msk_cluster.msk-cluster](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/msk_cluster) | resource |
| [aws_msk_configuration.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/msk_configuration) | resource |
| [aws_msk_scram_secret_association.this](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/msk_scram_secret_association) | resource |

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|------|---------|:--------:|
| <a name="input_broker_node_client_subnets"></a> [broker\_node\_client\_subnets](#input\_broker\_node\_client\_subnets) | A list of subnets to connect to in client VPC ([documentation](https://docs.aws.amazon.com/msk/1.0/apireference/clusters.html#clusters-prop-brokernodegroupinfo-clientsubnets)) | `list(string)` | `[]` | no |
| <a name="input_broker_node_ebs_volume_size"></a> [broker\_node\_ebs\_volume\_size](#input\_broker\_node\_ebs\_volume\_size) | The size in GiB of the EBS volume for the data drive on each broker node | `number` | `null` | no |
| <a name="input_broker_node_instance_type"></a> [broker\_node\_instance\_type](#input\_broker\_node\_instance\_type) | Specify the instance type to use for the kafka brokers. e.g. kafka.m5.large. ([Pricing info](https://aws.amazon.com/msk/pricing/)) | `string` | `null` | no |
| <a name="input_broker_node_security_groups"></a> [broker\_node\_security\_groups](#input\_broker\_node\_security\_groups) | A list of the security groups to associate with the elastic network interfaces to control who can communicate with the cluster | `list(string)` | `[]` | no |
| <a name="input_client_authentication_sasl_iam"></a> [client\_authentication\_sasl\_iam](#input\_client\_authentication\_sasl\_iam) | Enables IAM client authentication | `bool` | `false` | no |
| <a name="input_client_authentication_sasl_scram"></a> [client\_authentication\_sasl\_scram](#input\_client\_authentication\_sasl\_scram) | Enables SCRAM client authentication via AWS Secrets Manager | `bool` | `false` | no |
| <a name="input_client_authentication_tls_certificate_authority_arns"></a> [client\_authentication\_tls\_certificate\_authority\_arns](#input\_client\_authentication\_tls\_certificate\_authority\_arns) | List of ACM Certificate Authority Amazon Resource Names (ARNs) | `list(string)` | `[]` | no |
| <a name="input_cloudwatch_log_group_kms_key_id"></a> [cloudwatch\_log\_group\_kms\_key\_id](#input\_cloudwatch\_log\_group\_kms\_key\_id) | The ARN of the KMS Key to use when encrypting log data | `string` | `null` | no |
| <a name="input_cloudwatch_log_group_name"></a> [cloudwatch\_log\_group\_name](#input\_cloudwatch\_log\_group\_name) | Name of the Cloudwatch Log Group to deliver logs to | `string` | `null` | no |
| <a name="input_cloudwatch_log_group_retention_in_days"></a> [cloudwatch\_log\_group\_retention\_in\_days](#input\_cloudwatch\_log\_group\_retention\_in\_days) | Specifies the number of days you want to retain log events in the log group | `number` | `0` | no |
| <a name="input_cloudwatch_logs_enabled"></a> [cloudwatch\_logs\_enabled](#input\_cloudwatch\_logs\_enabled) | Indicates whether you want to enable or disable streaming broker logs to Cloudwatch Logs | `bool` | `false` | no |
| <a name="input_configuration_description"></a> [configuration\_description](#input\_configuration\_description) | Description of the configuration | `string` | `"Complete example configuration"` | no |
| <a name="input_configuration_server_properties"></a> [configuration\_server\_properties](#input\_configuration\_server\_properties) | Contents of the server.properties file. Supported properties are documented in the [MSK Developer Guide](https://docs.aws.amazon.com/msk/latest/developerguide/msk-configuration-properties.html) | `map(string)` | `{}` | no |
| <a name="input_create_cloudwatch_log_group"></a> [create\_cloudwatch\_log\_group](#input\_create\_cloudwatch\_log\_group) | Determines whether to create a CloudWatch log group | `bool` | `true` | no |
| <a name="input_create_schema_registry"></a> [create\_schema\_registry](#input\_create\_schema\_registry) | Determines whether to create a Glue schema registry for managing Avro schemas for the cluster | `bool` | `true` | no |
| <a name="input_create_scram_secret_association"></a> [create\_scram\_secret\_association](#input\_create\_scram\_secret\_association) | Determines whether to create SASL/SCRAM secret association | `bool` | `false` | no |
| <a name="input_encryption_at_rest_kms_key_arn"></a> [encryption\_at\_rest\_kms\_key\_arn](#input\_encryption\_at\_rest\_kms\_key\_arn) | You may specify a KMS key short ID or ARN (it will always output an ARN) to use for encrypting your data at rest. If no key is specified, an AWS managed KMS ('aws/msk' managed service) key will be used for encrypting the data at rest | `string` | `null` | no |
| <a name="input_encryption_in_transit_client_broker"></a> [encryption\_in\_transit\_client\_broker](#input\_encryption\_in\_transit\_client\_broker) | Encryption setting for data in transit between clients and brokers. Valid values: `TLS`, `TLS_PLAINTEXT`, and `PLAINTEXT`. Default value is `TLS` | `string` | `null` | no |
| <a name="input_encryption_in_transit_in_cluster"></a> [encryption\_in\_transit\_in\_cluster](#input\_encryption\_in\_transit\_in\_cluster) | Whether data communication among broker nodes is encrypted. Default value: `true` | `bool` | `null` | no |
| <a name="input_enhanced_monitoring"></a> [enhanced\_monitoring](#input\_enhanced\_monitoring) | Specify the desired enhanced MSK CloudWatch monitoring level. See [Monitoring Amazon MSK with Amazon CloudWatch](https://docs.aws.amazon.com/msk/latest/developerguide/monitoring.html) | `string` | `"PER_TOPIC_PER_PARTITION"` | no |
| <a name="input_environment"></a> [environment](#input\_environment) | Environment (e.g. `prod`, `dev`, `staging`). | `string` | `""` | no |
| <a name="input_firehose_delivery_stream"></a> [firehose\_delivery\_stream](#input\_firehose\_delivery\_stream) | Name of the Kinesis Data Firehose delivery stream to deliver logs to | `string` | `null` | no |
| <a name="input_firehose_logs_enabled"></a> [firehose\_logs\_enabled](#input\_firehose\_logs\_enabled) | Indicates whether you want to enable or disable streaming broker logs to Kinesis Data Firehose | `bool` | `false` | no |
| <a name="input_jmx_exporter_enabled"></a> [jmx\_exporter\_enabled](#input\_jmx\_exporter\_enabled) | Indicates whether you want to enable or disable the JMX Exporter | `bool` | `false` | no |
| <a name="input_kafka_broker_number"></a> [kafka\_broker\_number](#input\_kafka\_broker\_number) | Kafka brokers per zone | `number` | `1` | no |
| <a name="input_kafka_version"></a> [kafka\_version](#input\_kafka\_version) | Version of Kafka brokers | `string` | `"2.2.1"` | no |
| <a name="input_label_order"></a> [label\_order](#input\_label\_order) | Label order, e.g. `name`,`application`. | `list(any)` | <pre>[<br>  "name",<br>  "environment"<br>]</pre> | no |
| <a name="input_managedby"></a> [managedby](#input\_managedby) | ManagedBy, eg 'yadavprakash' | `string` | `"yadavprakash"` | no |
| <a name="input_msk_cluster_enabled"></a> [msk\_cluster\_enabled](#input\_msk\_cluster\_enabled) | Flag to control the msk-cluster creation. | `bool` | `true` | no |
| <a name="input_name"></a> [name](#input\_name) | Name  (e.g. `app` or `cluster`). | `string` | `""` | no |
| <a name="input_node_exporter_enabled"></a> [node\_exporter\_enabled](#input\_node\_exporter\_enabled) | Indicates whether you want to enable or disable the Node Exporter | `bool` | `false` | no |
| <a name="input_s3_logs_bucket"></a> [s3\_logs\_bucket](#input\_s3\_logs\_bucket) | Name of the S3 bucket to deliver logs to | `string` | `null` | no |
| <a name="input_s3_logs_enabled"></a> [s3\_logs\_enabled](#input\_s3\_logs\_enabled) | Indicates whether you want to enable or disable streaming broker logs to S3 | `bool` | `false` | no |
| <a name="input_s3_logs_prefix"></a> [s3\_logs\_prefix](#input\_s3\_logs\_prefix) | Prefix to append to the folder name | `string` | `null` | no |
| <a name="input_scaling_max_capacity"></a> [scaling\_max\_capacity](#input\_scaling\_max\_capacity) | Max storage capacity for Kafka broker autoscaling | `number` | `250` | no |
| <a name="input_scaling_role_arn"></a> [scaling\_role\_arn](#input\_scaling\_role\_arn) | The ARN of the IAM role that allows Application AutoScaling to modify your scalable target on your behalf. This defaults to an IAM Service-Linked Role | `string` | `null` | no |
| <a name="input_scaling_target_value"></a> [scaling\_target\_value](#input\_scaling\_target\_value) | The Kafka broker storage utilization at which scaling is initiated | `number` | `70` | no |
| <a name="input_schema_registries"></a> [schema\_registries](#input\_schema\_registries) | A map of schema registries to be created | `map(any)` | `{}` | no |
| <a name="input_schemas"></a> [schemas](#input\_schemas) | A map schemas to be created within the schema registry | `map(any)` | `{}` | no |
| <a name="input_scram_secret_association_secret_arn_list"></a> [scram\_secret\_association\_secret\_arn\_list](#input\_scram\_secret\_association\_secret\_arn\_list) | List of AWS Secrets Manager secret ARNs to associate with SCRAM | `list(string)` | <pre>[<br>  ""<br>]</pre> | no |
| <a name="input_timeouts"></a> [timeouts](#input\_timeouts) | Create, update, and delete timeout configurations for the cluster | `map(string)` | `{}` | no |

## Outputs

| Name | Description |
|------|-------------|
| <a name="output_arn"></a> [arn](#output\_arn) | Amazon Resource Name (ARN) of the MSK cluster |
| <a name="output_configuration_arn"></a> [configuration\_arn](#output\_configuration\_arn) | Amazon Resource Name (ARN) of the configuration |
| <a name="output_configuration_latest_revision"></a> [configuration\_latest\_revision](#output\_configuration\_latest\_revision) | Latest revision of the configuration |
| <a name="output_current_version"></a> [current\_version](#output\_current\_version) | Current version of the MSK Cluster used for updates, e.g. `K13V1IB3VIYZZH` |
| <a name="output_scram_secret_association_id"></a> [scram\_secret\_association\_id](#output\_scram\_secret\_association\_id) | Amazon Resource Name (ARN) of the MSK cluster |
<!-- END_TF_DOCS -->