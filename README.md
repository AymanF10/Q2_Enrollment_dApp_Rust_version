```markdown
# Turbin3 Enrollment dApp - Solana Devnet Integration

A Rust-based Solana devnet integration for managing wallet operations and enrollment in the Turbin3 prerequisite program.

---

## 📖 Overview
This project enables users to:
1. Generate Solana keypairs
2. Airdrop devnet SOL tokens
3. Transfer tokens to Turbin3 wallets
4. Interact with the Turbin3 enrollment program using PDAs (Program Derived Addresses) and IDLs (Interface Definition Language)

---

## 🚀 Features
- **Keypair Generation**: Securely create Solana wallets
- **Devnet Token Management**: Airdrop and transfer SOL
- **PDA Operations**: Program-derived address creation
- **IDL Integration**: Interface with on-chain programs
- **Transaction Automation**: Batch operations for enrollment

---

## ⚙️ Prerequisites
- [Rust & Cargo](https://www.rust-lang.org/tools/install) (v1.65+)
- Solana CLI (optional for advanced operations)
- Turbin3 public key (provided during course registration)

---

## 🛠️ Installation
```bash
# Initialize project
cargo init --lib

# Add dependencies to Cargo.toml
[dependencies]
solana-sdk = "1.15.2"
solana-client = "1.15.2"
solana-program = "1.15.2"
bs58 = "0.5.0"
borsh = "0.10.3"

# For IDL generation
solana-idlgen = { git = "https://github.com/deanmlittle/solana-idlgen.git" }
```

---

## 🧑💻 Usage

### 1. Generate Keypair
```rust
#[test]
fn keygen() {
    let kp = Keypair::new();
    println!("Wallet: {}", kp.pubkey());
    println!("Private Key: {:?}", kp.to_bytes());
}
```
Run with:
```bash
cargo test keygen -- --nocapture
```

### 2. Airdrop Devnet SOL
```rust
const RPC_URL: &str = "https://api.devnet.solana.com";

#[test]
fn airdrop() {
    let keypair = read_keypair_file("dev-wallet.json").unwrap();
    let client = RpcClient::new(RPC_URL);
    client.request_airdrop(&keypair.pubkey(), 2_000_000_000).unwrap();
}
```

### 3. Transfer to Turbin3 Wallet
```rust
let to_pubkey = Pubkey::from_str("<TURBIN3_PUBKEY>").unwrap();
let transaction = Transaction::new_signed_with_payer(
    &[transfer(&keypair.pubkey(), &to_pubkey, 1_000_000)],
    Some(&keypair.pubkey()),
    &vec![&keypair],
    recent_blockhash
);
```

### 4. Empty Devnet Wallet
```rust
let balance = rpc_client.get_balance(&keypair.pubkey()).unwrap();
let fee = rpc_client.get_fee_for_message(&message).unwrap();
let transaction = Transaction::new_signed_with_payer(
    &[transfer(&keypair.pubkey(), &to_pubkey, balance - fee)],
    Some(&keypair.pubkey()),
    &vec![&keypair],
    recent_blockhash
);
```

---

## 📂 Project Structure
```
src/
├── lib.rs           # Core logic
├── programs/
│   ├── mod.rs       # Program modules
│   └── Turbin3_prereq.rs  # IDL-generated code
dev-wallet.json      # Generated keypair
.gitignore           # Excludes wallet files
```

---

## 🔗 Interacting with Turbin3 Program
1. **IDL Setup**:
   ```rust
   idlgen!({
       "metadata": {"address": "HC2oqz2p6DEWfrahenqdq2moUcga9c9biqRBcdK3XKU1"},
       "instructions": [{ "name": "complete", /*...*/ }]
   });
   ```

2. **PDA Creation**:
   ```rust
   let prereq = Turbin3PrereqProgram::derive_program_address(
       &[b"prereq", signer.pubkey().as_ref()],
       &program_id
   );
   ```

3. **Enrollment Transaction**:
   ```rust
   let args = CompleteArgs { github: b"your_github".to_vec() };
   let transaction = Turbin3PrereqProgram::complete(
       &[&signer.pubkey(), &prereq, &system_program::id()],
       &args,
       Some(&signer.pubkey()),
       &[&signer],
       blockhash
   );
   ```

---

## 🤝 Contributing
1. Fork repository
2. Create feature branch (`git checkout -b feature/improvement`)
3. Commit changes (`git commit -m 'Add feature'`)
4. Push to branch (`git push origin feature/improvement`)
5. Open Pull Request

---

## 📜 License
MIT License - See [LICENSE.md](LICENSE.md) for details.






