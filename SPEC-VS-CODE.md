## HASIL HUNT-01 - 25 Nov 2026 - CLOSED, BUKAN BUG
- Hipotesis: Payer pakai 0x7A, sender pakai 0x79, mismatch
- Bukti git grep: Tidak ada 0x7A di base_tx / evm/base_tx.rs
- Kesimpulan: Kode konsisten pakai [0x79, 0x00] / [0x79, 0x01] / [0x79, 0x02]. Docs yang outdated.
- Status: JANGAN jadikan laporan. Tutup hipotesis ini.

# Spec vs Code - Status: Belum Bisa Dibilang Bug
## Mismatch 1 - Signature Prefix
Kode: SENDER [0x79, 0x00], PAYER [0x79, 0x01], REPLAY [0x79, 0x02]
Docs: sender_hash pakai 0x79, payer_hash pakai 0x7A
Status: MISMATCH, perlu cek di executor mana yang dipakai. Bisa jadi docs outdated.
## Koreci Hermes
- is_nonce_free = nonce_key == U256::MAX, bukan != 0
- MAX_ACCOUNT_CHANGES_PER_TX = 1, bukan 256
- MAX_CALL_PHASES_PER_TX = 1024, bukan calls
- TxContext tidak punya get_caller()