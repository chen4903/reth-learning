# Call_Flow

[step 1](https://github.com/bluealloy/revm/blob/main/crates/interpreter/src/interpreter_action.rs#L23)

```rust
pub enum InterpreterAction {
    /// CALL, CALLCODE, DELEGATECALL, STATICCALL
    /// or EOF EXT instuction called.
    Call { inputs: Box<CallInputs> },
    /// CREATE or CREATE2 instruction called.
    Create { inputs: Box<CreateInputs> },
    /// EOF CREATE instruction called.
    EOFCreate { inputs: Box<EOFCreateInput> },
    /// Interpreter finished execution.
    Return { result: InterpreterResult },
    /// No action
    #[default]
    None,
}
```

[step 2](https://github.com/bluealloy/revm/blob/main/crates/interpreter/src/interpreter_action/call_inputs.rs#L8-L43)

```rust
pub struct CallInputs {
    /// The call data of the call.
    pub input: Bytes,
    /// The return memory offset where the output of the call is written.
    ///
    /// In EOF, this range is invalid as EOF calls do not write output to memory.
    pub return_memory_offset: Range<usize>,
    /// The gas limit of the call.
    pub gas_limit: u64,
    /// The account address of bytecode that is going to be executed.
    ///
    /// Previously `context.code_address`.
    pub bytecode_address: Address,
    /// Target address, this account storage is going to be modified.
    ///
    /// Previously `context.address`.
    pub target_address: Address,
    /// This caller is invoking the call.
    ///
    /// Previously `context.caller`.
    pub caller: Address,
    /// Call value.
    ///
    /// NOTE: This value may not necessarily be transferred from caller to callee, see [`CallValue`].
    ///
    /// Previously `transfer.value` or `context.apparent_value`.
    pub value: CallValue,
    /// The call scheme.
    ///
    /// Previously `context.scheme`.
    pub scheme: CallScheme,
    /// Whether the call is initiated inside a static call.
    pub is_static: bool,
    /// Whether the call is initiated from EOF bytecode.
    pub is_eof: bool,
}
```

[step 3](https://github.com/bluealloy/revm/blob/main/crates/interpreter/src/interpreter_action/call_inputs.rs#L124-L133)

```rust
pub enum CallScheme {
    /// `CALL`.
    Call,
    /// `CALLCODE`
    CallCode,
    /// `DELEGATECALL`
    DelegateCall,
    /// `STATICCALL`
    StaticCall,
}
```





























