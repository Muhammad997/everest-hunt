# Inventory Terverifikasi dari git show
- base_tx/tx.rs: TxBase, nonce_key: U256, nonce-free hanya jika nonce_key == NONCE_KEY_MAX
- base_tx/constants.rs: BASE_TX_TYPE = 0x79, NONCE_KEY_MAX = U256::MAX, SENDER_SIGNATURE_TYPE = [0x79, 0x00], PAYER_SIGNATURE_TYPE = [0x79, 0x01], REPLAY_ID_TYPE = [0x79, 0x02], OPEN_PAYER = Address::ZERO, NONCE_FREE_MAX_EXPIRY_WINDOW = 20000 ms, MAX_ACCOUNT_CHANGES_PER_TX = 1, MAX_CALL_PHASES_PER_TX = 1024
- precompiles/nonce/dispatch.rs (137 baris): hanya getNonce yang ABI-exposed, mutasi hanya dari execution layer
- precompiles/tx_context/dispatch.rs (195 baris): hanya 3 getter: getTransactionSender, getTransactionPayer, getTransactionSenderActorId
- evm/base_tx.rs, evm/handler.rs, evm/metered_gas.rs: executor BaseTx