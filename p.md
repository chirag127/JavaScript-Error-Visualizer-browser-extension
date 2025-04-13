Okay, here is a Product Requirements Document (PRD) for the "JavaScript Error Visualizer" browser extension, designed as a final product specification based on your requirements.

Product Requirements Document: JavaScript Error Visualizer

Version: 1.0
Date: October 26, 2023
Author: [Your Name/Team Name]
Status: Final

1. Introduction

This document outlines the requirements for the JavaScript Error Visualizer browser extension. Current web development debugging practices heavily rely on the browser's developer console to identify and understand JavaScript errors. While functional, this often requires developers to manually correlate error messages and stack traces with specific elements or code sections within the webpage's structure. The JavaScript Error Visualizer aims to significantly improve this workflow by directly highlighting the DOM elements associated with runtime JavaScript errors on the webpage itself, providing immediate visual context and accelerating the debugging process. This extension operates entirely client-side within the browser using Manifest V3 standards.

2. Goals & Objectives

Improve Debugging Efficiency: Reduce the time developers spend identifying the source element or area of a webpage affected by a JavaScript error.

Enhance Intuition: Provide immediate visual feedback directly on the webpage, making the connection between an error and its visual manifestation more intuitive.

Contextualize Errors: Display relevant error information (message, stack trace snippet) directly alongside the highlighted element.

Support Modern Development: Accurately map errors from transpiled/minified code back to the original source code using source maps.

Seamless Integration: Offer a non-intrusive user experience that can be easily toggled on/off.

3. Target Audience

Web Developers (Frontend & Full-Stack): Primary users who actively write and debug JavaScript code for web applications.

QA Engineers / Testers: Users responsible for identifying and reporting bugs in web applications, who can use the visualizer to provide better context in bug reports.

Technical Support Staff: Users who may need to diagnose frontend issues reported by end-users.

4. Use Cases & User Stories

UC-1: Basic Error Highlighting: As a developer, when a JavaScript error occurs due to manipulating a specific DOM element (e.g., null.appendChild), I want the extension to visually highlight that element (or its closest identifiable parent) on the page so that I can immediately see where the error originated visually.

UC-2: Event Listener Error: As a developer, when an error occurs within an event listener attached to an element (e.g., a button click handler fails), I want the extension to highlight the element that triggered the event so that I can quickly identify the interactive component causing the issue.

UC-3: Error Information Tooltip: As a developer, when hovering over a highlighted element, I want to see a tooltip displaying the specific error message and a relevant snippet of the stack trace (including source-mapped location if available) so that I can understand the error without immediately switching to the console.

UC-4: Source Map Integration: As a developer working with bundled/minified code (e.g., React, Vue, Angular builds), when an error occurs, I want the extension to use available source maps to pinpoint the error location in my original source code within the tooltip/panel, so that I can debug using my familiar codebase.

UC-5: Toggling Visualization: As a developer, I want to be able to easily enable or disable the error visualization feature via a toolbar icon so that I can control when the highlighting occurs and avoid visual clutter when not actively debugging.

UC-6: Handling Multiple Errors: As a developer, when multiple errors occur, I want the extension to highlight all associated elements (potentially with different visual cues or aggregated information) so that I can get a comprehensive overview of issues on the page.

UC-7: Clearing Highlights: As a developer, I want a way to manually clear all current error highlights from the page so that I can reset the view after fixing an issue or during testing.

5. Functional Requirements

FR-1: Error Interception
* The extension MUST intercept uncaught JavaScript runtime errors (window.onerror).
* The extension MUST intercept unhandled promise rejections (window.onunhandledrejection).
* The extension MUST be able to capture errors logged via console.error() (configurable, perhaps opt-in, as this can be noisy).

FR-2: Element Identification & Association
* The extension MUST analyze the error object and its stack trace to identify the most likely associated DOM element(s).
* Strategies for element identification should include:
* Analyzing event.target if the error occurred within an event handler.
* Parsing stack traces for function calls known to interact with specific elements (requires heuristics).
* Attempting to find elements mentioned in error messages (e.g., selectors in errors from libraries).
* If a direct element cannot be found, highlight the <body> or provide a clear indication the error isn't tied to a specific visible element.
* The extension MUST handle errors occurring within Web Workers or iframes where possible, potentially indicating the context boundary.

FR-3: Visual Highlighting
* Identified elements MUST be visually highlighted directly on the webpage.
* The highlighting mechanism MUST be configurable (e.g., border color, style, overlay color/opacity). Default: A distinct, noticeable but non-obtrusive red border or semi-transparent overlay.
* Highlighting MUST adapt to element visibility and layout changes where feasible.
* Multiple errors associated with the same element should potentially intensify the highlight or be indicated in the tooltip.

FR-4: Information Display (Tooltip/Panel)
* Hovering over a highlighted element MUST display detailed error information.
* Displayed information MUST include:
* Error message.
* Error type (e.g., TypeError, ReferenceError).
* Stack trace snippet, prioritizing the most relevant frames.
* Source-mapped file name and line number (if source map available and parsed successfully).
* Timestamp of the error occurrence.
* An optional, more detailed panel (accessible via the toolbar icon) MAY display a list of all captured errors for the current page load, allowing users to click an error to focus/re-highlight the corresponding element.

FR-5: Source Map Processing
* The extension MUST detect the presence of source maps (//# sourceMappingURL=...).
* The extension MUST fetch and parse source maps to map stack trace locations (file, line, column) back to the original source code.
* Source map parsing MUST occur efficiently, potentially in the background service worker, to avoid blocking the main thread.
* Handle cases where source maps are unavailable or fail to parse gracefully.

FR-6: User Interface & Controls
* A browser toolbar icon MUST be provided.
* The toolbar icon MUST indicate the extension's status (e.g., active/inactive, errors detected on the page).
* Default state: Active, Grey icon.
* Errors detected: Active, Red icon with badge count of errors.
* Inactive: Grey icon, potentially with a slash.
* Clicking the toolbar icon MUST provide access to:
* Enable/Disable toggle for the visualization on the current tab/all tabs (configurable scope).
* A button to "Clear Highlights" on the current page.
* Link to a Settings/Options page.
* (Optional) The detailed error list panel mentioned in FR-4.

FR-7: Settings/Options Page
* A dedicated options page MUST allow users to configure:
* Enable/Disable the extension globally.
* Customize highlighting appearance (color, style, overlay opacity).
* Option to enable/disable interception of console.error.
* Option to configure the scope of the enable/disable toggle (current tab vs. all tabs).
* Option to blacklist/whitelist specific domains where the extension should/shouldn't run.
* Option to configure persistence of highlights across page reloads (Default: Off).

FR-8: Persistence
* User settings MUST persist across browser sessions using chrome.storage.local or chrome.storage.sync.
* Error highlights are NOT persisted across page reloads by default but can be enabled via settings (FR-7).

6. Non-Functional Requirements

NFR-1: Performance
* The extension MUST have minimal impact on webpage loading and rendering performance.
* Error interception and processing MUST be highly efficient. DOM manipulation for highlighting should be optimized.
* Source map fetching and parsing MUST NOT significantly block page interactivity.

NFR-2: Accuracy
* Element identification MUST be reasonably accurate for common error patterns.
* Source map parsing MUST correctly map locations for supported source map versions.

NFR-3: Security
* The extension MUST operate strictly within the permissions granted by Manifest V3 (activeTab, scripting, storage, potentially webNavigation for persistence across loads).
* No sensitive user data (beyond settings and temporary error info) should be stored or transmitted.
* Content scripts must be carefully designed to avoid introducing security vulnerabilities on the host page.

NFR-4: Usability
* The interface (toolbar icon, tooltips, settings) MUST be intuitive and easy to understand.
* Visual highlighting should be clear but not overly disruptive to the page layout.

NFR-5: Compatibility
* The extension MUST be compatible with the latest stable versions of Google Chrome, Mozilla Firefox, and Microsoft Edge.
* The extension should function correctly on a wide variety of websites and web applications, including SPAs (Single Page Applications) built with popular frameworks (React, Vue, Angular, etc.).

NFR-6: Reliability
* The extension must be stable and handle edge cases gracefully (e.g., complex DOM structures, errors in dynamically loaded scripts, errors during page unload).

7. Design & UI/UX Considerations

Highlighting: Use CSS outline or box-shadow for borders to minimize layout shifts. Consider subtle animations on highlight appearance/disappearance. Use ::before or ::after pseudo-elements for overlays if needed.

Tooltips: Use clean, readable fonts. Ensure tooltips don't obscure critical page elements. Position tooltips intelligently based on element location.

Toolbar Icon: Use clear visual states (active, inactive, error). The error count badge should be easily readable.

Settings Page: Organize options logically using standard HTML form elements. Provide clear labels and descriptions for each setting.

8. Technical Specifications

Platform: Browser Extension (Manifest V3).

Manifest File (manifest.json):

manifest_version: 3

name: "JavaScript Error Visualizer"

version: "1.0"

description: "Visually highlights elements associated with JavaScript errors directly on the webpage."

permissions: [activeTab, scripting, storage] (Potentially webNavigation if reload persistence is implemented robustly).

background: { service_worker: "background.js" } (For source map fetching/parsing, central state management).

content_scripts: [{ matches: ["<all_urls>"], js: ["content.js"], css: ["highlighter.css"], run_at: "document_start" }] (Injects error listeners and highlighting logic). run_at: "document_start" is crucial to catch early errors.

action: { default_popup: "popup.html", default_icon: { ... } }

options_page: "options.html"

icons: { ... }

Frontend (extension/):

popup.html, popup.css, popup.js: Toolbar popup UI and logic.

options.html, options.css, options.js: Settings page UI and logic.

content.js: Content script responsible for injecting error listeners, communicating with the background script, receiving commands, and manipulating the DOM to apply highlights/tooltips.

highlighter.css: CSS rules for visual highlighting and tooltips.

Background (extension/):

background.js: Service worker for handling messages from content/popup scripts, managing state (enabled/disabled status, settings), performing source map fetching and parsing (using libraries like source-map-js), potentially managing error aggregation.

Backend (backend/):

Not Required: For the core functionality described in this PRD, no external backend server is necessary. All processing (error interception, source mapping, highlighting) occurs within the user's browser.

Libraries: Consider using a lightweight library for source map parsing (e.g., source-map-js). Avoid heavy frameworks in content scripts to minimize performance impact.

9. Data & Privacy

The extension will store user preferences (settings) locally using chrome.storage.local or chrome.storage.sync.

Error information (messages, stack traces) is processed locally within the browser for the purpose of visualization and source mapping.

No personally identifiable information (PII), browsing history, or specific error details will be transmitted to any external servers.

If source maps are fetched, requests will be made directly from the user's browser to the URL specified in the source code, mimicking standard browser behavior.

10. Release Criteria / Definition of Done

All Functional Requirements (FR-1 to FR-8) are implemented and tested.

All Non-Functional Requirements (NFR-1 to NFR-6) are met and verified.

The extension passes testing on the latest stable versions of Chrome, Firefox, and Edge.

The extension functions correctly on a representative sample of diverse websites (static sites, SPAs, sites with complex DOMs).

Source map parsing is functional and accurate for common bundling tools (Webpack, Rollup, Parcel).

UI/UX is polished, intuitive, and consistent.

No critical or major bugs remain open.

Privacy policy is clearly stated (if required by stores, even for local processing).

Code is reviewed and adheres to quality standards.

11. Key Considerations During Implementation

Performance on Complex Pages: Develop strategies to handle scenarios with thousands of DOM nodes or frequent errors without freezing the browser. Debouncing/throttling error processing might be necessary.

Ambiguous Element Association: Define clear heuristics or fallback mechanisms when a stack trace does not clearly point to a single DOM element.

Shadow DOM: Investigate and implement support for identifying and highlighting elements within Shadow DOM boundaries.

Dynamic Content: Ensure highlighting logic correctly handles elements that are dynamically added, removed, or modified after the initial page load.

Cross-Origin Iframes: Error reporting and DOM access are restricted by default. Determine the best approach to visualize errors originating from cross-origin iframes (likely limited).