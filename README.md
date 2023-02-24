# drone-jacoco-s3

Drone plugin to upload jacoco code coverage reports to AWS S3 bucket and publish the bucket static site url to `Artifacts` tab under `Pipieline Execution`.

## Build

Build the binary with the following commands:

```bash
go build
```

## Docker

Build the Docker image with the following commands:

```
./hacking/build.sh
docker buildx build -t DOCKER_ORG/drone-jacoco-s3 --platform linux/amd64 .
```

Please note incorrectly building the image for the correct x64 linux and with
CGO disabled will result in an error when running the Docker image:

```
docker: Error response from daemon: Container command
'/bin/drone-jacoco-s3' not found or does not exist..
```

## Usage

```bash
docker run --rm \
  -e PLUGIN_AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID} \
  -e PLUGIN_AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY} \
  -e PLUGIN_AWS_DEFAULT_REGION=ap-southeast-2 \
  -e PLUGIN_AWS_BUCKET=bucket-name \
  -e PLUGIN_REPORT_SOURCE=maven-code-coverage/target/site/jacoco \
  -e PLUGIN_ARTIFACT_FILE=url.txt \
  -v $(pwd):$(pwd) \
  -w $(pwd) \
  harnesscommunity/drone-jacoco-s3
```

In Harness CI,
```yaml
              - step:
                  type: Plugin
                  name: Publish Jacoco Metadata
                  identifier: custom_plugin
                  spec:
                    connectorRef: account.harnessImage
                    image: harnesscommunity/drone-jacoco-s3
                    settings:
                      aws_access_key_id: <+pipeline.variables.AWS_ACCESS_KEY_ID>
                      aws_secret_access_key: <+pipeline.variables.AWS_SECRET_ACCESS_KEY>
                      aws_default_region: ap-southeast-2
                      aws_bucket: bucket-name
                      artifact_file: url.txt
                      report_source: maven-code-coverage/target/site/jacoco
```

To get the list of supported arguments:
```bash
go build

./drone-jacoco-s3 --help
```
```
NAME:
   drone-jacoco-s3
...
...
GLOBAL OPTIONS:
   --pipeline-sequence-id value  Harness CIE Pipeline Sequence ID [$PLUGIN_PIPELINE_SID]
   --aws-access-key value        AWS Access Key ID [$PLUGIN_AWS_ACCESS_KEY_ID]
   --aws-secret-key value        AWS Secret Access Key [$PLUGIN_AWS_SECRET_ACCESS_KEY]
   --aws-default-region value    AWS Default Region [$PLUGIN_AWS_DEFAULT_REGION]
   --aws-bucket value            AWS Default Region [$PLUGIN_AWS_BUCKET]
   --report-source value         AWS Default Region [$PLUGIN_REPORT_SOURCE]
   --artifact-file value         Artifact file [$PLUGIN_ARTIFACT_FILE]
...
...
```