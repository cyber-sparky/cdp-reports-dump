# Getting Started with jq

**`jq`** is a powerful command-line tool for working with JSON data. It simplifies filtering, transforming, and manipulating JSON directly from the terminal.

## Basic Usage and Help

To get started with `jq`, you can view the help documentation using:

```bash
jq --help
```

This command provides a comprehensive list of options and usage examples.

## Simple Filtering

Let's start with a basic example where we filter and pretty-print a simple JSON object:

```bash
echo '{"cloudprovider":{"name":"AWS","url":"www.aws.com"}}' | jq '.'
```

**Output:**
```json
{
  "cloudprovider": {
    "name": "AWS",
    "url": "www.aws.com"
  }
}
```

## Using `jq` with API Responses

You can directly apply `jq` filters to API responses to format and filter the JSON data:

```bash
curl https://randomuser.me/api/ | jq '.'
```

**Output:**
```json
{
  "results": [
    {
      "gender": "male",
      "name": {
        "title": "Mr",
        "first": "Lance",
        "last": "Craig"
      },
      ...
    }
  ],
  "info": {
    "seed": "489f7f19e861af8d",
    "results": 1,
    "page": 1,
    "version": "1.3"
  }
}
```

## Working with JSON Files

Suppose you have a JSON file named `learnjq.json`. You can create and prettify this file using `jq`.

**Create the JSON File:**

```bash
cat > learnjq.json << EOL
[
  {"details":{"name": "AWS","url": "www.amazon.com"},"services": [{"type": "CI-CD","name": "AWS CodeBuild"},{"type": "Secret management","name": "AWS Secrets Manager"}]},
  {"details": {"name": "Azure","url": "www.azure.com"},"services": [{"type": "CI-CD","name": "Azure DevOps"},{"type": "Secret management","name": "Azure Key Vault"}]},
  {"details": {"name": "GCP","url": "www.cloud.google.com"},"services": [{"type": "CI-CD","name": "Cloud Build"},{"type": "Secret management","name": "Secret Manager"}]}
]
EOL
```

**Read and Prettify the JSON File:**

```bash
jq '.' learnjq.json
```

**Output:**
```json
[
  {
    "details": {
      "name": "AWS",
      "url": "www.amazon.com"
    },
    "services": [
      {
        "type": "CI-CD",
        "name": "AWS CodeBuild"
      },
      {
        "type": "Secret management",
        "name": "AWS Secrets Manager"
      }
    ]
  },
  {
    "details": {
      "name": "Azure",
      "url": "www.azure.com"
    },
    "services": [
      {
        "type": "CI-CD",
        "name": "Azure DevOps"
      },
      {
        "type": "Secret management",
        "name": "Azure Key Vault"
      }
    ]
  },
  {
    "details": {
      "name": "GCP",
      "url": "www.cloud.google.com"
    },
    "services": [
      {
        "type": "CI-CD",
        "name": "Cloud Build"
      },
      {
        "type": "Secret management",
        "name": "Secret Manager"
      }
    ]
  }
]
```

# Accessing Properties

`jq`, a powerful command-line JSON processor, to filter and transform JSON data. By the end, you'll be able to read and manipulate JSON data easily from the command line.

### Prerequisites
Make sure `jq` is installed on your system. It's pre-installed on the DevSecOps-Box machine for this exercise. If you need to install it on another machine, follow the instructions here: [jq Installation](https://github.com/stedolan/jq/wiki/Installation).

### Basic Usage
To get started, let's look at a simple JSON and use `jq` to process it.

1. **Basic JSON Pretty-Print**
   ```bash
   echo '{"cloudprovider":{"name":"AWS","url":"www.aws.com"}}' | jq '.'
   ```
   Output:
   ```json
   {
     "cloudprovider": {
       "name": "AWS",
       "url": "www.aws.com"
     }
   }
   ```
   The `.` filter outputs the JSON data unchanged, but in a prettified format.

## Filtering JSON Data
We often need to filter specific properties from JSON data. Here's how to access properties using the `.field` operator.

2. **Accessing a Nested Property**
   ```bash
   echo '{"cloudprovider":{"name":"AWS","url":"www.aws.com"}}' | jq '.cloudprovider.name'
   ```
   Output:
   ```json
   "AWS"
   ```

## Handling JSON Arrays
Let's work with arrays in JSON data. We'll use a sample JSON file `learnjq.json` containing details about cloud providers and their services.

3. **Creating the JSON File**
   ```bash
   cat > learnjq.json << EOL
   [
     {"details":{"name": "AWS","url": "www.amazon.com"},"services": [{"type": "CI-CD","name": "AWS CodeBuild"},{"type": "Secret management","name": "AWS Secrets Manager"}]},
     {"details": {"name": "Azure","url": "www.azure.com"},"services": [{"type": "CI-CD","name": "Azure DevOps"},{"type": "Secret management","name": "Azure Key Vault"}]},
     {"details": {"name": "GCP","url": "www.cloud.google.com"},"services": [{"type": "CI-CD","name": "Cloud Build"},{"type": "Secret management","name": "Secret Manager"}]}
   ]
   EOL
   ```

4. **Reading the JSON File**
   ```bash
   jq '.' learnjq.json
   ```

5. **Accessing Elements in an Array**
   ```bash
   jq '.[] | .details' learnjq.json
   ```
   Output:
   ```json
   {
     "name": "AWS",
     "url": "www.amazon.com"
   }
   {
     "name": "Azure",
     "url": "www.azure.com"
   }
   {
     "name": "GCP",
     "url": "www.cloud.google.com"
   }
   ```

6. **Extracting Specific Fields**
   ```bash
   jq '.[] | {name: .details.name, url: .details.url}' learnjq.json
   ```
   Output:
   ```json
   {
     "name": "AWS",
     "url": "www.amazon.com"
   }
   {
     "name": "Azure",
     "url": "www.azure.com"
   }
   {
     "name": "GCP",
     "url": "www.cloud.google.com"
   }
   ```

7. **Accessing Array by Index**
   ```bash
   jq '.[1] | {name: .details.name, url: .details.url}' learnjq.json
   ```
   Output:
   ```json
   {
     "name": "Azure",
     "url": "www.azure.com"
   }
   ```

8. **Grouping Services by Provider**
   ```bash
   jq '.[] | {name: .details.name, url: .details.url, services: [.services[]]}' learnjq.json
   ```
   Output:
   ```json
   {
     "name": "AWS",
     "url": "www.amazon.com",
     "services": [
       {
         "type": "CI-CD",
         "name": "AWS CodeBuild"
       },
       {
         "type": "Secret management",
         "name": "AWS Secrets Manager"
       }
     ]
   }
   {
     "name": "Azure",
     "url": "www.azure.com",
     "services": [
       {
         "type": "CI-CD",
         "name": "Azure DevOps"
       },
       {
         "type": "Secret management",
         "name": "Azure Key Vault"
       }
     ]
   }
   {
     "name": "GCP",
     "url": "www.cloud.google.com",
     "services": [
       {
         "type": "CI-CD",
         "name": "Cloud Build"
       },
       {
         "type": "Secret management",
         "name": "Secret Manager"
       }
     ]
   }
   ```

For more advanced usage and options, refer to the [jq manual](https://stedolan.github.io/jq/manual/).

# Using jq Functions

## Finding the Length
The `length` function in `jq` can be used to find the length of an array or the number of properties in an object. Here's an example of how to find the number of services each cloud provider offers.

```bash
jq '.[] | {name: .details.name, servicecount: .services | length}' learnjq.json
```

**Command Output:**
```json
{
  "name": "AWS",
  "servicecount": 2
}
{
  "name": "Azure",
  "servicecount": 2
}
{
  "name": "GCP",
  "servicecount": 2
}
```

In this example, we were able to get the service count for each cloud provider using the `length` function in `jq`.

## Selecting Values
The `select` function in `jq` allows us to filter data based on certain conditions. For instance, to find details only for the cloud provider AWS:

```bash
jq '.[] | select(.details.name == "AWS")' learnjq.json
```

**Command Output:**
```json
{
  "details": {
    "name": "AWS",
    "url": "www.amazon.com"
  },
  "services": [
    {
      "type": "CI-CD",
      "name": "AWS CodeBuild"
    },
    {
      "type": "Secret management",
      "name": "AWS Secrets Manager"
    }
  ]
}
```

In this output, we see the details for AWS only.

## Challenge: Filtering Cloud Providers with CI-CD Service
To display the results for cloud providers that have a CI-CD service using the `learnjq.json` file, we can use a combination of `select` and other `jq` functions.

```bash
jq '.[] | select(.services[].type == "CI-CD") | {name: .details.name, url: .details.url, services: [.services[] | select(.type == "CI-CD")]}' learnjq.json
```

**Command Output:**
```json
{
  "name": "AWS",
  "url": "www.amazon.com",
  "services": [
    {
      "type": "CI-CD",
      "name": "AWS CodeBuild"
    }
  ]
}
{
  "name": "Azure",
  "url": "www.azure.com",
  "services": [
    {
      "type": "CI-CD",
      "name": "Azure DevOps"
    }
  ]
}
{
  "name": "GCP",
  "url": "www.cloud.google.com",
  "services": [
    {
      "type": "CI-CD",
      "name": "Cloud Build"
    }
  ]
}
```

### Explanation:
- `.[]` iterates over each cloud provider in the array.
- `select(.services[].type == "CI-CD")` filters cloud providers that have a CI-CD service.
- `{name: .details.name, url: .details.url, services: [.services[] | select(.type == "CI-CD")]}` constructs an object with the `name`, `url`, and `services` containing only CI-CD services.

# Challenge

1. How would you filter the cloud provider name that has CI-CD service in the learnjq.json file.

```bash
/# cat learnjq.json | jq -r '.[] | select(.services[].type == "CI-CD") | {name: .details.name}'
{
  "name": "AWS"
}
{
  "name": "Azure"
}
{
  "name": "GCP"
} 
```

2. Show us the result of CI/CD service name that provided by each cloud provider.

```bash 
/# cat learnjq.json | jq -r '.[] | select(.services[].type == "CI-CD") | {name: .details.name, servicename: .services
[].name}'
{
  "name": "AWS",
  "servicename": "AWS CodeBuild"
}
{
  "name": "AWS",
  "servicename": "AWS Secrets Manager"
}
{
  "name": "Azure",
  "servicename": "Azure DevOps"
}
{
  "name": "Azure",
  "servicename": "Azure Key Vault"
}
{
  "name": "GCP",
  "servicename": "Cloud Build"
}
{
  "name": "GCP",
  "servicename": "Secret Manager"
}
```