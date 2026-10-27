Gemini Bridge Pro 1.7.7

UI refinement, same version: Quota and tests by account uses a responsive account-card grid and starts collapsed with Show/Hide controls. The panel has more separation from the blocks above, consistent body padding and more space around account actions. Wide windows show multiple accounts per row; narrow windows use one column.

- AI Studio Test all tests every active Google account or one selected account. Each account uses its own isolated endpoint for Chat, Responses and function calling.
- Results are saved by account with model, time, sent prompt and reply. Quota limits, rejected sessions, unsupported APIs and other failures have separate labels. Paused accounts are skipped.
- Accounts provides Test all accounts and individual Test actions. The shared pool pauses during isolated testing and is restored afterward; restoration errors remain visible.
- Successful tests verify availability at that time. They do not measure remaining quota. Results from other models, backend folders or more than 24 hours ago require retesting.
- Guide credit now reads Created by DesignMD Generator.

Windows installer users can install over the existing installation with the same Windows account and location. Portable users should extract the entire new ZIP. Account data and settings remain in their existing data folder.
