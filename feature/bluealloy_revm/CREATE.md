# CREATE

There are two ways to create a new contract in solidity: CREATE and CREATE2

## CREATE

[step 1](https://github.com/bluealloy/revm/blob/main/crates/revm/src/context/inner_evm_context.rs#L379)

```rust
// Create address
let mut init_code_hash = B256::ZERO;
let created_address = match inputs.scheme {
    CreateScheme::Create => inputs.caller.create(old_nonce), // Here
    CreateScheme::Create2 { salt } => {
        init_code_hash = keccak256(&inputs.init_code);
        inputs.caller.create2(salt.to_be_bytes(), init_code_hash)
    }
};
```

[step 2](https://github.com/alloy-rs/core/blob/main/crates/primitives/src/bits/address.rs#L338-L365)

```rust
pub fn create(&self, nonce: u64) -> Self {
    use alloy_rlp::{Encodable, EMPTY_LIST_CODE, EMPTY_STRING_CODE};

    // max u64 encoded length is `1 + u64::BYTES`
    const MAX_LEN: usize = 1 + (1 + 20) + 9;

    let len = 22 + nonce.length();
    debug_assert!(len <= MAX_LEN);

    let mut out = [0u8; MAX_LEN];

    // list header
    // minus 1 to account for the list header itself
    out[0] = EMPTY_LIST_CODE + len as u8 - 1;

    // address header + address
    out[1] = EMPTY_STRING_CODE + 20;
    out[2..22].copy_from_slice(self.as_slice());

    // nonce
    nonce.encode(&mut &mut out[22..]);

    let hash = keccak256(&out[..len]);
    Self::from_word(hash)
}
```

## CREATE2

[step 1](https://github.com/bluealloy/revm/blob/main/crates/revm/src/context/inner_evm_context.rs#L379)

```rust
// Create address
let mut init_code_hash = B256::ZERO;
let created_address = match inputs.scheme {
    CreateScheme::Create => inputs.caller.create(old_nonce), // Here
    CreateScheme::Create2 { salt } => {
        init_code_hash = keccak256(&inputs.init_code);
        inputs.caller.create2(salt.to_be_bytes(), init_code_hash)
    }
};
```

[step 2](https://github.com/alloy-rs/core/blob/main/crates/primitives/src/bits/address.rs#L420-L427)

```rust
#[must_use]
pub fn create2<S, H>(&self, salt: S, init_code_hash: H) -> Self
where
// not `AsRef` because `[u8; N]` does not implement `AsRef<[u8; N]>`
S: Borrow<[u8; 32]>,
H: Borrow<[u8; 32]>,
{
    self._create2(salt.borrow(), init_code_hash.borrow())
}
```

[step 3](https://github.com/alloy-rs/core/blob/main/crates/primitives/src/bits/address.rs#L429-L440)

```rust
// non-generic inner function
fn _create2(&self, salt: &[u8; 32], init_code_hash: &[u8; 32]) -> Self {
    // note: creating a temporary buffer and copying everything over performs
    // much better than calling `Keccak::update` multiple times
    let mut bytes = [0; 85];
    bytes[0] = 0xff;
    bytes[1..21].copy_from_slice(self.as_slice());
    bytes[21..53].copy_from_slice(salt);
    bytes[53..85].copy_from_slice(init_code_hash);
    let hash = keccak256(bytes);
    Self::from_word(hash)
}
```

