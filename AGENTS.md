# Moqueue

Ruby gem for mocking the AMQP library in tests. Allows testing AMQP-based code without running a broker by providing mock replacements for queues, exchanges, and channels.

## Tech Stack

- **Language:** Ruby (see `moqueue.gemspec` for dependencies)
- **Type:** Gem (consumed via Bundler)
- **Key dependency:** `amqp` >= 0.9.0

## Local Development & Testing

Run `bundle install` and `bundle exec rake spec`. Test framework: RSpec.

## Key Directories

| Path | Purpose |
|------|---------|
| `lib/moqueue/` | Mock implementations (MockQueue, MockExchange, MockChannel) |
| `spec/` | RSpec suite |

## Architecture Notes

- Provides `overload_amqp` to globally replace AMQP classes with mocks
- Captures published messages in a `routed_messages` array for assertions
- Used by other Custom Ink services (e.g. journal-service) for testing AMQP workflows
