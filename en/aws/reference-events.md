# @events

## `@events` define SNS topics and Lambda handlers for them. 

This `.arc` file defines two `@events`:

```arc
@app
testapp

@events
hit-counter
likes
```

Which generates the corresponding code:

```bash
/
|-- events
|   |-- hit-counter/
|   `-- likes/
|-- .arc
`-- package.json
```

And the following deployment lambdas:

- `testapp-staging-hit-counter`
- `testapp-production-hit-counter`
- `testapp-staging-likes`
- `testapp-production-likes`

## Next: [Scheduling functions with `@scheduled`](/reference/scheduled)
