Gemini Bridge Pro 1.8.1

Fixes a stale manual-endpoint record incorrectly blocking account tests and pool controls after Windows reuses its PID for another process.

- Manual endpoint ownership now checks the executable, account/listener arguments and recorded process creation time. An unrelated process using an old PID does not count as an active endpoint and is never stopped as part of recovery.
- Tasks & parallel shows Stop endpoints & restore pool above disabled mode controls when recovery is needed. This action stops owned endpoints and restores the recorded shared pool. Active requests and running task queues remain protected.
- The manual endpoint section opens when recovery is needed even if an earlier preference had collapsed it. Owned endpoints with an unreadable admin API remain visible with an error instead of misleading Start controls.
- The interface explains that Single/Parallel is saved pool capacity; it does not automatically run a task queue.
- Both 9Router and OmniRoute support from 1.8.0 remains available.

Verification includes recycled-PID regression cases, fixture upstreams, production browser checks and the built Windows application. These checks do not claim live Google quota or authentication verification.

Windows installer users can install over the existing installation with the same Windows account and location. Portable users should extract the entire new ZIP. Account data and settings remain in their existing data folder.
