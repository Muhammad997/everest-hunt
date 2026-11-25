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

## HASIL HUNT-04 - 25 Nov 2026 - CLOSED, BUKAN BUG
- Hipotesis: Version downgrade via caller, grant_role_unchecked bisa dipanggil siapa saja, scheduled multiplier bisa dimanipulasi
- Bukti: versions.rs from_base_upgrade dari hardfork, bukan caller. dispatch grant_role_unchecked hanya untuk Factory bootstrap saat belum ada admin. grant_role normal cek ensure_role_admin_mutations_available + ensure_role sebelum manggil unchecked. updateUIMultiplier cek effective_at > now, <= u64::MAX, no live overlap, requires operator role jika tidak privileged, MAX = uint128.max, toUIAmount pakai effective multiplier.
- Kesimpulan: B20 Asset version routing + role + scheduled multiplier sudah benar. Tutup HUNT-04.

## HASIL HUNT-05 - 25 Nov 2026 - CLOSED, BUKAN BUG
- Hipotesis: Composite bisa circular / nested, inverted bisa bypass deny
- Bukti: require_child_policy_in_range [2,4], validate 2-pass PolicyNotFound > InvalidChildPolicy, child harus simple custom, builtin/composite ditolak. V3 inverted = XOR bit, base_policy_id strip sebelum cek, inverted composite ditolak, inverted unknown fails closed, inverted negate simple/composite/builtin.
- Kesimpulan: Policy composite + inverted sudah benar. Tutup HUNT-05.

## HASIL HUNT-06 - 25 Nov 2026 - CLOSED, BUKAN BUG
- Hipotesis: Variant bisa dipilih caller untuk bypass, activation bisa re-call untuk reset
- Bukti: Variant hanya Asset=0/Stablecoin=1, from_abi/from_discriminant tolak invalid, getB20Address revert untuk invalid variant. create_b20_decoded: from_abi -> ensure_activated(variant.activation_feature) -> decode -> check_version==1. Activation set_activated: activate yang sudah active revert, deactivate yang sudah inactive revert, repeated_transition_reverts, require_admin_caller.
- Kesimpulan: Factory Variant + Activation sudah benar. Tutup HUNT-06.
