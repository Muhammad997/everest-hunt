## HASIL HUNT-01 - 25 Nov 2026 - CLOSED, BUKAN BUG
- Hipotesis: Payer pakai 0x7A, sender pakai 0x79, mismatch
- Bukti git grep: Tidak ada 0x7A di base_tx / evm/base_tx.rs
- Kesimpulan: Kode konsisten pakai [0x79, 0x00] / [0x79, 0x01] / [0x79, 0x02]. Docs yang outdated.
- Status: JANGAN jadikan laporan. Tutup hipotesis ini.

## HASIL HUNT-02 - 25 Nov 2026 - CLOSED, BUKAN BUG
- Hipotesis: Executor tidak implement phase, atau phase yang revert tidak di-skip
- Bukti: base_tx.rs baris 1243 loop per phase, checkpoint_commit, committed_phases += 1, phase_reverted -> skip semua phase berikutnya. Test later_phase_skipped_after_earlier_phase_reverts dan committed_phase_warms_later_phase lolos di kode.
- Thread-local TxBaseCommittedPhases sudah ada clear() di awal execute dan set() di akhir, untuk cegah misattribution antar tx.
- Kesimpulan: Phase handling sudah benar. Tutup hipotesis ini.

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

## HUNT-03 VERIFIKASI AKHIR - 25 Nov 2026 - CLOSED FINAL
- affordability.rs: unaffordable() return true = unaffordable = skip. Kalau resolved_payer gagal, return true, jadi tx di-skip, bukan dimasukkan.
- Kesimpulan: Semua 6 pemanggil resolved_payer aman. HUNT-03 ditutup final, tidak ada bug payer spoof.