In Playwright (and other browser automation tools), **headless** means running the browser **without a visible UI window** — basically, the browser runs in the background without showing any graphical interface on your screen.

### Why use headless mode?
- **Faster execution**: No need to render the UI, so tests run quicker.
- **CI/CD friendly**: Headless mode is great for running automated tests on servers or build pipelines where no display is available.
- **Resource efficient**: Uses less CPU and memory compared to full browser mode.

### When might you use headless = false?
- When debugging tests, you want to see what the browser is doing visually.
- When you want to take screenshots or manually observe the browser's behavior during automation.
  
- [6] So **headless = true** means the browser runs in the background without showing up on screen. **headless = false** means you see the browser window while the script runs.

