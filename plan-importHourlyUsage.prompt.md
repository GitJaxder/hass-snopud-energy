## Plan: Import Hourly SnoPUD History

Interpret SnoPUD portal timestamps as `America/Los_Angeles`, request hourly CSV data, and write each interval into the existing external Home Assistant statistics used by the Energy Dashboard. Live sensors remain unchanged.

**Steps**

1. Update [`coordinator.py`](custom_components/snopud_energy/coordinator.py) to request `INTERVAL_HOURLY` on normal and re-login fetches.
2. Update [`snopud_api.py`](custom_components/snopud_energy/snopud_api.py) to preserve hourly timestamps, support portal timestamp formats, sort by full timestamp, and maintain current-month totals.
3. Update `_async_import_statistics()` to use each reading’s historical hour as `StatisticData.start`, with timezone-aware timestamps and cumulative energy/cost sums.
4. Extend [`test_statistics_import.py`](tests/test_statistics_import.py) with hourly, ordering, deduplication, malformed timestamp, DST, and cumulative-sum coverage.
5. Adjust [`conftest.py`](tests/conftest.py) datetime stubs if needed.
6. Update [`README.md`](README.md) from daily-history wording to hourly-history wording.
7. Reuse the existing `INTERVAL_HOURLY` constant; no new entity or statistic IDs are needed.

**Decisions**

- Use existing external statistics: `snopud_energy:energy_consumption` and `snopud_energy:energy_cost`.
- Interpret timezone-less portal timestamps as Snohomish County local time.
- Preserve append-only deduplication against the latest imported timestamp.
- Keep date-only parsing as a compatibility fallback.
- Leave live sensor behavior unchanged.

**Verification**

- Run focused tests with `pytest -q tests/test_statistics_import.py`.
- Run `ruff check custom_components tests` and `mypy custom_components` if available.
- Verify hourly timestamps, cumulative sums, request interval selection, DST handling, and updated README guidance.

The detailed plan is saved in [`/memories/session/plan.md`](/memories/session/plan.md).