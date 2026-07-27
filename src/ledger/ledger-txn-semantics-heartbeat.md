$$
\newcommand \Fee {\mathrm{fee}}
\newcommand \MinTxnFee {T_{\Fee,\min}}
\newcommand \RekeyTo {\mathrm{RekeyTo}}
\newcommand \Heartbeat {\mathrm{hb}}
\newcommand \PayoutsChallengeBits {\Heartbeat_\mathrm{bits}}
\newcommand \PayoutsChallengeGracePeriod {\Heartbeat_\mathrm{grace}}
\newcommand \PayoutsChallengeInterval {\Heartbeat_r}
$$

# Heartbeat Transaction Semantics

If a [_heartbeat transaction_](./ledger-txn-heartbeat.md) sets the
[_heartbeat challenge discount_](./ledger-txn-heartbeat.md#heartbeat-challenge-discount)
flag (`c`), its required [_fee_](./ledger-transactions.md#fee) is reduced by
\\( \MinTxnFee \\), and the transaction **FAILS** to execute unless:

- The [_note_](./ledger-transactions.md#note) \\( N \\) is empty;

- The [_lease_](./ledger-transactions.md#lease) \\( x \\) is empty;

- The [_rekey to address_](./ledger-transactions.md#rekey-to) \\( \RekeyTo \\) is
empty;

- The [_heartbeat_address_](./ledger-txn-heartbeat.md#heartbeat-address), \\( a \\),
is `Online`;

- The [_heartbeat_address_](./ledger-txn-heartbeat.md#heartbeat-address), \\( a \\),
_eligibility_ flag (`ie`) is `True`;

- The [_heartbeat_address_](./ledger-txn-heartbeat.md#heartbeat-address), \\( a \\),
is _at risk_ of suspension.

Challenges begin only after the first full interval, so an account is never _at risk_
before round \\( \PayoutsChallengeInterval \\). For a current round
\\( r \geq \PayoutsChallengeInterval \\), let the _challenge round_ \\( c \\) be the
most recent round that is \\( 0 \mod \PayoutsChallengeInterval \\), that is
\\( c = r - (r \bmod \PayoutsChallengeInterval) \\).

An account is _at risk_ of suspension if

$$
\frac{\PayoutsChallengeGracePeriod}{2} < r - c \leq \PayoutsChallengeGracePeriod,
$$

the [_block seed_](./ledger-block.md#seed) of round \\( c \\) matches \\( a \\) in the
first \\( \PayoutsChallengeBits \\) bits, and

$$
\max(a.\mathrm{LastProposed}, a.\mathrm{LastHeartbeat}) < c.
$$

No account is _at risk_ if the `Payouts` consensus parameters in effect at round
\\( c \\) differ from those in effect at round \\( r \\) (see the
[Block Rewards](./ledger-parameters.md#block-rewards) parameters).

If successful, the `LastHeartbeat` of the specified heartbeat address \\( a \\)
is updated to the current round.
