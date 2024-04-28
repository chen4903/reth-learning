Core traits

- Inspector: Hooks are called during various stage of EVM executions
  - set a Logger,
  - print gas cost,
  - print state,
  - ...
- Database: mutable interaction with the DB
- DatabaseRef: read-only interactions
- DatabaseCommit: commit changes to DB.
- Host: definitions for all the methods that any EVM host must implement, bridge between EVM and env.