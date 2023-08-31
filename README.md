# prisma-service

## Problem
Using `AWS SAM` to build a Typescript Lambda Function that includes Prisma as a dependency results in the following error message when the Lambda is invoked:

```
errorType: PrismaClientInitializationError

errorMessage: Prisma Client could not find its `schema.prisma`. This is likely caused by a bundling step, which leads to `schema.prisma` not being copied near the resulting bundle.
```


## How to Reproduce
1. Generate Hello World Typescript Lambda Project Example via `sam init` and follow prompt for setting up a Typescript Lambda Function.

```bash
$ sam init

You can preselect a particular runtime or package type when using the `sam init` experience.
Call `sam init --help` to learn more.

Which template source would you like to use?
        1 - AWS Quick Start Templates
        2 - Custom Template Location
Choice: 1

Choose an AWS Quick Start application template
        1 - Hello World Example
        2 - Data processing
        3 - Hello World Example with Powertools for AWS Lambda
        4 - Multi-step workflow
        5 - Scheduled task
        6 - Standalone function
        7 - Serverless API
        8 - Infrastructure event management
        9 - Lambda Response Streaming
        10 - Serverless Connector Hello World Example
        11 - Multi-step workflow with Connectors
        12 - GraphQLApi Hello World Example
        13 - Full Stack
        14 - Lambda EFS example
        15 - Hello World Example With Powertools for AWS Lambda
        16 - DynamoDB Example
        17 - Machine Learning
Template: 1

Use the most popular runtime and package type? (Python and zip) [y/N]: n

Which runtime would you like to use?
        1 - aot.dotnet7 (provided.al2)
        2 - dotnet6
        3 - go1.x
        4 - go (provided.al2)
        5 - graalvm.java11 (provided.al2)
        6 - graalvm.java17 (provided.al2)
        7 - java17
        8 - java11
        9 - java8.al2
        10 - java8
        11 - nodejs18.x
        12 - nodejs16.x
        13 - nodejs14.x
        14 - python3.9
        15 - python3.8
        16 - python3.7
        17 - python3.11
        18 - python3.10
        19 - ruby3.2
        20 - ruby2.7
        21 - rust (provided.al2)
Runtime: 11

What package type would you like to use?
        1 - Zip
        2 - Image
Package type: 1

Based on your selections, the only dependency manager available is npm.
We will proceed copying the template using npm.

Select your starter template
        1 - Hello World Example
        2 - Hello World Example TypeScript
Template: 2

Would you like to enable X-Ray tracing on the function(s) in your application?  [y/N]: y
X-Ray will incur an additional cost. View https://aws.amazon.com/xray/pricing/ for more details

Would you like to enable monitoring using CloudWatch Application Insights?
For more info, please view https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-application-insights.html [y/N]: y
AppInsights monitoring may incur additional cost. View https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/appinsights-what-is.html#appinsights-pricing for more details

Project name [sam-app]: prisma-service
                                                                                                               
Cloning from https://github.com/aws/aws-sam-cli-app-templates (process may take a moment)                      

    -----------------------
    Generating application:
    -----------------------
    Name: prisma-service
    Runtime: nodejs18.x
    Architectures: x86_64
    Dependency Manager: npm
    Application Template: hello-world-typescript
    Output Directory: .
    Configuration file: prisma-service/samconfig.toml
    
    Next steps can be found in the README file at prisma-service/README.md
        

Commands you can use next
=========================
[*] Create pipeline: cd prisma-service && sam pipeline init --bootstrap
[*] Validate SAM template: cd prisma-service && sam validate
[*] Test Function in the Cloud: cd prisma-service && sam sync --stack-name {stack-name} --watch
```

2. Reorganized project structure and renamed some folders. *Do not forget to update the `CodeUri` property in the `template.yaml`.*
3. Navigate to `my-rest-api` and run `npm install`
```bash
$ cd my-rest-api/
$ npm install

added 406 packages, and audited 407 packages in 21s

58 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
(sam-cli) kimmie@kimmie-mac my-rest-api (initial-commit) $ npm install prisma --save-dev

added 2 packages, and audited 409 packages in 4s

58 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

4. Initialize Prisma by running `npx prisma init`. This creates `my-rest-api/prisma/schema.prisma`
```bash
$ npx prisma init

✔ Your Prisma schema was created at prisma/schema.prisma
  You can now open it in your favorite editor.

Next steps:
1. Set the DATABASE_URL in the .env file to point to your existing database. If your database has no tables yet, read https://pris.ly/d/getting-started
2. Set the provider of the datasource block in schema.prisma to match your database: postgresql, mysql, sqlite, sqlserver, mongodb or cockroachdb.
3. Run prisma db pull to turn your database schema into a Prisma schema.
4. Run prisma generate to generate the Prisma Client. You can then start querying your database.

More information in our documentation:
https://pris.ly/d/getting-started
    
$ npx prisma db pull
Prisma schema loaded from prisma/schema.prisma
Environment variables loaded from .env
Datasource "db": PostgreSQL database "mydb", schema "mydb_schema" at "localhost:5432"

✔ Introspected 1 model and wrote it into prisma/schema.prisma in 1.42s
      
Run prisma generate to generate Prisma Client.
```

5. Update `schema.prisma` with the database model.

6. Generate Prisma Client by running `npx prisma generate`
```bash
$ npx prisma generate
Environment variables loaded from .env
Prisma schema loaded from prisma/schema.prisma
Warning: Your current platform `darwin` is not included in your generator's `binaryTargets` configuration ["rhel-openssl-1.0.x"].
To fix it, use this generator config in your schema.prisma:
generator client {
  provider      = "prisma-client-js"
  binaryTargets = ["native", "rhel-openssl-1.0.x"]
}
Note, that by providing `native`, Prisma Client automatically resolves `darwin`.
Read more about deploying Prisma Client: https://www.prisma.io/docs/reference/tools-and-interfaces/prisma-schema/generators


added 2 packages, and audited 411 packages in 4s

58 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

✔ Installed the @prisma/client and prisma packages in your project

✔ Generated Prisma Client (v5.2.0) to ./node_modules/@prisma/client in 179ms
Start using Prisma Client in Node.js (See: https://pris.ly/d/client)

import { PrismaClient } from '@prisma/client'
const prisma = new PrismaClient()

or start using Prisma Client at the edge (See: https://pris.ly/d/accelerate)

import { PrismaClient } from '@prisma/client/edge'
const prisma = new PrismaClient()


See other ways of importing Prisma Client: http://pris.ly/d/importing-client
```

7. Build the project by running `sam build`
```bash
$ sam build
Starting Build use cache                                                                                       
Manifest file is changed (new hash: 8d969f857cacd29175b8be9b394c0a7d) or dependency folder                     
(.aws-sam/deps/91fc3f5f-9e98-4c0a-b6af-9fece90c50be) is missing for (HelloWorldFunction), downloading          
dependencies and copying/building source                                                                       
Building codeuri: /Users/kimmie/kimmie_docs/personal-learning-stuff/sam-lambda-bundle-prisma/my-rest-api       
runtime: nodejs18.x metadata: {'BuildMethod': 'esbuild', 'BuildProperties': {'Minify': True, 'Target':         
'es2020', 'Sourcemap': True, 'EntryPoints': ['app.ts']}} architecture: x86_64 functions: HelloWorldFunction    

Running NodejsNpmEsbuildBuilder:CopySource                                                                     
Running NodejsNpmEsbuildBuilder:NpmInstall                                                                     
Running NodejsNpmEsbuildBuilder:EsbuildBundle                                                                  
Running NodejsNpmEsbuildBuilder:CleanUp                                                                        
Running NodejsNpmEsbuildBuilder:MoveDependencies                                                               
                                                                                                               
Sourcemap set without --enable-source-maps, adding --enable-source-maps to function HelloWorldFunction         
NODE_OPTIONS                                                                                                   
                                                                                                               
You are using source maps, note that this comes with a performance hit! Set Sourcemap to false and remove      
NODE_OPTIONS: --enable-source-maps to disable source maps.                                                     
                                                                                                               
Build Succeeded

Built Artifacts  : .aws-sam/build
Built Template   : .aws-sam/build/template.yaml

Commands you can use next
=========================
[*] Validate SAM template: sam validate
[*] Invoke Function: sam local invoke
[*] Test Function in the Cloud: sam sync --stack-name {{stack-name}} --watch
[*] Deploy: sam deploy --guided
```

7. Invoke the Lambda Function locally by running `sam local invoke`. This is where I get the error.
```bash
sam local invoke Invoking app.lambdaHandler (nodejs18x)                                                                        
Local image is up-to-date                                                                                      
Using local image: public.ecr.aws/lambda/nodejs:18-rapid-x86_64.                                               
                                                                                                               
Mounting                                                                                                       
/Users/kimmie/kimmie_docs/personal-learning-stuff/sam-lambda-bundle-prisma/.aws-sam/build/HelloWorldFunction as
/var/task:ro,delegated, inside runtime container                                                               
START RequestId: 6624182b-9692-419f-9cd5-95853a801feb Version: $LATEST
2023-08-31T17:49:08.567Z        undefined       ERROR   Uncaught Exception      {"errorType":"PrismaClientInitializationError","errorMessage":"Prisma Client could not find its `schema.prisma`. This is likely caused by a bundling step, which leads to `schema.prisma` not being copied near the resulting bundle. We would appreciate if you could take the time to share some information with us.\nPlease help us by answering a few questions: https://pris.ly/bundler-investigation-error","name":"PrismaClientInitializationError","clientVersion":"5.2.0","stack":["PrismaClientInitializationError: Prisma Client could not find its `schema.prisma`. This is likely caused by a bundling step, which leads to `schema.prisma` not being copied near the resulting bundle. We would appreciate if you could take the time to share some information with us.","Please help us by answering a few questions: https://pris.ly/bundler-investigation-error","    at pr (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:113:118)","    at $a (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:115:1541)","    at t (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:128:4564)","    at Object.<anonymous> (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/app.ts:4:22)","    at Module._compile (node:internal/modules/cjs/loader:1256:14)","    at Module._extensions..js (node:internal/modules/cjs/loader:1310:10)","    at Module.load (node:internal/modules/cjs/loader:1119:32)","    at Module._load (node:internal/modules/cjs/loader:960:12)","    at Module.require (node:internal/modules/cjs/loader:1143:19)","    at require (node:internal/modules/cjs/helpers:121:18)"]}
31 Aug 2023 17:49:09,012 [ERROR] (rapid) Init failed error=Runtime exited with error: exit status 129 InvokeID=
2023-08-31T17:49:09.650Z        undefined       ERROR   Uncaught Exception      {"errorType":"PrismaClientInitializationError","errorMessage":"Prisma Client could not find its `schema.prisma`. This is likely caused by a bundling step, which leads to `schema.prisma` not being copied near the resulting bundle. We would appreciate if you could take the time to share some information with us.\nPlease help us by answering a few questions: https://pris.ly/bundler-investigation-error","name":"PrismaClientInitializationError","clientVersion":"5.2.0","stack":["PrismaClientInitializationError: Prisma Client could not find its `schema.prisma`. This is likely caused by a bundling step, which leads to `schema.prisma` not being copied near the resulting bundle. We would appreciate if you could take the time to share some information with us.","Please help us by answering a few questions: https://pris.ly/bundler-investigation-error","    at pr (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:113:118)","    at $a (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:115:1541)","    at t (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:128:4564)","    at Object.<anonymous> (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/app.ts:4:22)","    at Module._compile (node:internal/modules/cjs/loader:1256:14)","    at Module._extensions..js (node:internal/modules/cjs/loader:1310:10)","    at Module.load (node:internal/modules/cjs/loader:1119:32)","    at Module._load (node:internal/modules/cjs/loader:960:12)","    at Module.require (node:internal/modules/cjs/loader:1143:19)","    at require (node:internal/modules/cjs/helpers:121:18)"]}
END RequestId: 2572e971-2065-4bb2-b9c9-9cf14ecb40be
REPORT RequestId: 2572e971-2065-4bb2-b9c9-9cf14ecb40be  Init Duration: 1.74 ms  Duration: 2937.24 ms    Billed Duration: 2938 ms       Memory Size: 128 MB     Max Memory Used: 128 MB
{"errorType": "PrismaClientInitializationError", "errorMessage": "Prisma Client could not find its `schema.prisma`. This is likely caused by a bundling step, which leads to `schema.prisma` not being copied near the resulting bundle. We would appreciate if you could take the time to share some information with us.\nPlease help us by answering a few questions: https://pris.ly/bundler-investigation-error", "trace": ["PrismaClientInitializationError: Prisma Client could not find its `schema.prisma`. This is likely caused by a bundling step, which leads to `schema.prisma` not being copied near the resulting bundle. We would appreciate if you could take the time to share some information with us.", "Please help us by answering a few questions: https://pris.ly/bundler-investigation-error", "    at pr (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:113:118)", "    at $a (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:115:1541)", "    at t (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/node_modules/@prisma/client/runtime/library.js:128:4564)", "    at Object.<anonymous> (/private/var/folders/3_/rjfyry8d0tv1w_cj2b8fzzjm0000gn/T/tmp9g5j81f1/app.ts:4:22)", "    at Module._compile (node:internal/modules/cjs/loader:1256:14)", "    at Module._extensions..js (node:internal/modules/cjs/loader:1310:10)", "    at Module.load (node:internal/modules/cjs/loader:1119:32)", "    at Module._load (node:internal/modules/cjs/loader:960:12)", "    at Module.require (node:internal/modules/cjs/loader:1143:19)", "    at require (node:internal/modules/cjs/helpers:121:18)"]}(sam-cli) kimmie@kimmie-mac sam-lambda-bundle-pr
```