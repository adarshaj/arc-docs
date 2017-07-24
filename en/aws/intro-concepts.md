# Concepts

Cloud functions surface a unique set of entirely new problems:

- Configuration tooling was designed for the last generation of computing metaphors (and often lags behind the releases of new functionality)
- AWS is massive and overwhelming with many similar, but not the same, products
- The web console is confusing, with divergent interfaces between interlocking services
- Deep proprietary knowledge is required to configure and maintain common infrastructure primatives
- Configuration and infrastructure can drift, leaving systems in states that are difficult to repeat / reproduce, and thus scale
- Painful manifest files; JSON is difficult to read, has no comments, and unforgiving to edit; YAML isn't much better and especially worse with deeply nested statements

_Some_ of these problems have been tamed with [infrastructure as code](https://en.wikipedia.org/wiki/Infrastructure_as_Code), creating repeatable and reproducable systems.

The tradeoff is you are committing AWS configuration knowledge into your revision control systems.

**`.arc` views infrastructure as a build artifact.** And we prefer to not check build artifacts in with our code.

## The .arc file

`architect` defines a simplistic plaintext format, `.arc`, as a manfiest file to solve the specific problems laid out above.

With `architect`, you can:

- Focus on defining your app architecture with a subset of service primatives as high level defintions
- Use `npm scripts` to  generate local code, configure, provision, and deploy cloud infrastructure from the `.arc` manifest
- You can still safely use the console tactically to access and administer primatives defined in `.arc`
- The format, parser, and tooling are completely open to extension

> In theory, `.arc` is also entirely portable between cloud vendors. (However no ports to clouds other than AWS have been made as of today.)

## The .arc format

The `.arc` format follows a few simple rules:

- Comments start with `#`
- Sections start with `@`
- **Everything between sections becomes instructions for generating AWS infrastructure**

`.arc` files are made up of the following sections: 

- `@app` defines the application namespace
- `@html` section defines html routes (API Gateway and Lambda)
- `@json`  defines json routes (API Gateway and Lambda)
- `@events` defines application events you can publish and subscribe to (SNS)
- `@scheduled` defines functions that run on a timed schedule (Cloudwatch Events)
- `@tables` defines database tables and trigger functions for them (DynamoDB)
- `@indexes` defines additional database table indexes (DynamoDB)

This is a complete `.arc` file example: 

```arc
# .arc
@app
hello

@html
get /
post /likes

@json
get /likes

@events
hit-counter

@scheduled
daily-affirmation rate(1 day)

@tables
likes
  likeID *String
  update Lambda

@indexes
likes
  date *String
```

Running `npm run create` in the same directory as the `.arc` file above generates the following function code:

```bash
/
├──src
│  ├──html
│  │  ├──get-index/
│  │  └──get-likes/
│  ├──json
│  │  └──get-likes/
│  ├──events
│  │  └──hit-counter/
│  ├──scheduled
│  │  └──daily-affirmation/
│  └──tables
│     └──likes-update/
├──.arc
└──package.json
```

The code was also immediately deployed to the cloud in isolated `staging` and `production` environments.

`architect` ships additional `npm run` workflows for deployment, working offline, and dependency management.

The `.arc` format is terse, easy to read, and quickly learnable to author. The intent expressed by a `.arc` file unlocks the formerly complex tasks of cloud infrastructure provisioning, deployment, and orchestration.

## Implementing principles and practices

`architect` follows many of the principles pioneered by agile and championed by devops. Versioning infrastructure. Tight feedback loops for dev, while maintaining isolation between stages. Systems that are consistent, inspectable, transparent, and exensible. 

### Architecture as text

- `.arc` manifest file defines achitecture elements as plainly as possible in text
- Nesting and syntax is deliberately constrained
- And you can add comments!

### Repeatable and consistent builds

- `arc-create` only creates, and never destroys; it skips if what it was to generate already exists
- Per above, `arc-create` is intended to be run and re-run as your system changes and grows
- Use the AWS console to administer (i.e. remove infrastructure) or script destructive actions yourself; `architect` never destroys

### Delivery is isolated from deployment

- `arc-sandbox` allows you to work locally and offline from the cloud 
- `arc-deploy` treats `staging` and `production` as first class concepts
- Deployment to `production` requires an extra manual step of setting `ARC_DEPLOY=production` env var

### Extensible and flexibile systems

- `arc-parser` is open, and `architect` tooling ignores `@sections` it does not know
- Therefore, you can use `.arc` with your own `npm` scripts
- `arc-modules` lets you act on all `.arc` defined modules with common `npm` commands

## Next steps

These principles and the practices that follow them are just a starting point; expect our understanding to grow and change as we learn more.

Next up: [read about .arc limitations](/intro/limits).
