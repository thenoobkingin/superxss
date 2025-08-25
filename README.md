[![Releases](https://img.shields.io/github/v/release/thenoobkingin/superxss?label=Releases&color=purple)](https://github.com/thenoobkingin/superxss/releases)

SuperXSS Payload Lab — Weird XSS Patterns & Tools Playground

🚨 This repository centers on cross-site scripting payload concepts, test cases, and a curated payload catalog for security research. A release asset is available at the Releases page and may be downloaded and executed in a controlled test environment for safe analysis: https://github.com/thenoobkingin/superxss/releases

Hero image
![XSS Lab](https://user-images.githubusercontent.com/583231/32284805-e2a7c4a2-bf0b-11e7-8f9f-6d86f8a3a4f3.png)

Table of contents
- About
- Key goals
- What you will find here
- XSS types and simple theory
- Payload families and patterns (high level)
- Encoding, normalization, and canonicalization
- Safe test environment guide
- Test harness and usage notes
- File layout and release notes
- Contribute
- Issue & PR workflow
- Glossary
- License
- Acknowledgments

About
This repo collects weird and unusual XSS payload concepts and test cases. It aims to serve security testers, developers, and researchers. The content focuses on pattern design, edge cases, filter interaction, and encoding effects. The project documents ideas and organizes a catalog. It pairs concepts with test harnesses ready for lab use.

Key goals
- Map odd and surprising XSS vectors that trigger in modern browsers.
- Show interaction between input filters and browser parsers.
- Provide a compact catalog of payload patterns, grouped by family.
- Offer test harnesses you can run in an isolated lab.
- Encourage reproducible test cases and community contribution.

What you will find here
- A catalog of payload patterns grouped by target parsing context.
- High-level explanation for why each family matters.
- Test harness code to reproduce behavior in a lab.
- Scripts to run automated checks in a safe environment.
- Release assets for offline analysis. The release page lists downloadable files that you may download and execute in a test lab: https://github.com/thenoobkingin/superxss/releases

How to read this repo
- Start in docs/ to read patterns and reasoning.
- Use examples/ for non-destructive, read-only demonstrations.
- Use lab/ for local test harnesses. Run these only in isolated networks and local browsers started in testing mode.
- Check RELEASES for packaged test assets. Those assets may include a ready-to-run harness for local test environments.

XSS types and simple theory
This section explains XSS in plain language. The goal is to let you reason about vectors without diving into exploit strings. Keep tests safe. The section uses simple terms and short sentences.

Reflected XSS
- The server returns user input inside a response.
- The response may include input in HTML, in attributes, or in scripts.
- If the browser treats input as code, it may execute code. That leads to a reflected XSS case.

Stored XSS
- The server saves user input.
- The saved input appears later in pages seen by other users.
- If stored input contains harmful tokens, the browser may execute them later.

DOM-based XSS
- The client-side script reads input from the URL or DOM.
- The script writes that input back to the page without safe handling.
- The risk appears only when the browser runs a page with the bad script.

Context matters
- Injection in plain HTML differs from injection in attributes or scripts.
- The parser treats each context differently.
- A payload that works in one context may fail in another.

Why odd payloads matter
- Filters may only check specific token patterns.
- Encodings and parser quirks can bypass naive filters.
- New browser features may expose unexpected parsing paths.
- Rarely seen byte sequences or unusual case-mixes may trigger parser differences.

Payload families and patterns (high level)
This section lists families of payload concepts. It avoids raw exploit strings. It shows the shape and intent of each family. Use this as a design guide for test cases.

1) Tag injection families
- Simple tags: place minimal tag-like input where the parser accepts tags.
- Broken tags: craft inputs that look like incomplete tags to influence parser state.
- Nested tags: inject closing and opening tags to alter DOM structure.
- Attribute terminations: inject sequences to break out of attributes.

2) Attribute context families
- Attribute value breaks: inject sequences that close an attribute and add new attributes.
- Event handler injection: add or alter on* attributes so the browser runs code.
- URI-based payloads: build values that use javascript: or data: URIs in safe contexts.
- Handler chain manipulation: inject attributes that combine with existing ones.

3) Script body families
- Script injection via inline script contexts.
- Breaking out of string literals inside scripts.
- Breaking out of template literals.
- Exploiting dynamic script construction functions.

4) CSS-based families
- Use CSS expression-like contexts in older browsers.
- Inject style attributes that cause unexpected parsing.
- Use CSS URL constructs to influence resource parsing.

5) URL and fragment families
- Use query strings to carry code to DOM-based sinks.
- Use fragment identifiers that client-side code may read.
- Use unusual encodings in the URL to affect client parsing.

6) Event and DOM API families
- The DOM may create elements from strings.
- Client code may use innerHTML, outerHTML, or insertAdjacentHTML.
- Focus on sinks where client code writes unsanitized input.

7) Encoding and unicode families
- Use mixed encodings to hit flaws in decoder logic.
- Use Unicode homoglyphs to bypass naive pattern checks.
- Use normalization differences between server and client.

8) Content-type mismatch families
- When the server mislabels content type, the browser may parse in a different mode.
- For example, a JSON blob served with text/html can trigger unexpected parsing.

9) Minimal and edge-case families
- Very short payloads can exploit naive checks.
- Payloads that only use a few bytes can still alter parser state.

10) Chained interactions
- Combine two minor transformations to reach an executable state.
- For example, one filter may decode percent-encoding, and a second may collapse whitespace.

Encoding, normalization, and canonicalization
This section describes how encoding layers change input shape. It explains where filters and parsers act.

Common layers
- Client-side encoding: URL encoding applied in links or forms.
- Server-side decoding: servers decode form input and then process it.
- Database encoding: the DB stores raw or encoded values.
- Output encoding: apps attempt to escape output before sending it to the browser.
- Browser decoding: the browser reinterprets encoded sequences when rendering.

Key ideas
- Two-phase decoding: an input that appears safe after one decode may become harmful after two decodes.
- Idempotence matters: repeated decodes must result in the right shape.
- Mixed encodings: a payload may use different encodings in parts.
- Unicode normalization: different normalization forms may compare differently.

Testing rules of thumb
- Test single encodings first.
- Then add layered encodings.
- Test with common normalization forms (NFC, NFD).
- Test mixed-case encodings and percent-encoded sequences.

Safe test environment guide
This guide shows how to set up a safe lab for browser-based testing. It uses plain steps. Do not test on third-party sites.

Network isolation
- Use a local machine that you own.
- Use VMs for isolation.
- Disable network access when possible.
- Use a local web server to host test pages.

Browser setup
- Use a standalone browser instance configured for testing.
- Run the browser with a new profile.
- Disable browser sync and account logins.
- Use automation only when it runs locally.

Local server
- Use a minimal HTTP server to serve test pages.
- Do not expose the server to the internet.
- Seed test pages with controlled hooks.

Tools to use
- Local web server (simple HTTP server, container, or minimal app).
- Browser developer tools.
- Headless browsers for automated checks in an isolated lab.
- Logging and snapshot tools to capture DOM state.

Data handling
- Store test results locally.
- Do not carry test payloads into production systems.
- Keep test artifacts in a separate workspace.

Test harness and usage notes
This repo includes a test harness for reproducing parsing states. The harness helps run test cases without complex setup. The release section contains packaged assets that include the harness. You may download and execute the release asset(s) in a controlled lab: https://github.com/thenoobkingin/superxss/releases

General usage model
- Clone the repo or download a release asset.
- Start the local web server inside a VM or sandbox.
- Open the harness page in a test browser instance.
- Select the payload family to test.
- Observe DOM changes and network activity.

Design notes for harnesses
- The harness separates source, filter, and sink.
- The harness lets testers toggle decoding steps.
- The harness logs raw input at each stage.
- The harness preserves raw bytes to detect encoding issues.

Examples of non-actionable experiments
The harness includes safe patterns that show how filters interact with parsers. Each experiment uses placeholders rather than executable code. The experiments let you see how:
- Attribute terminations change tag boundaries.
- Script contexts treat unclosed strings.
- URL fragments reach client-side sinks.
- Encoded sequences decode across layers.

File layout and release notes
This section lists the main files and folders. It helps you navigate the project.

Top-level layout
- README.md — this file.
- docs/ — design notes, pattern families, and theory.
- examples/ — static examples and visualizations.
- lab/ — test harness code for local testing.
- scripts/ — helper scripts for running the harness.
- releases/ — packaged assets for offline use (also posted to GitHub Releases).
- .github/ — issue templates and CI configs.
- LICENSE — license text.
- CONTRIBUTING.md — contribution guide.

Releases
- Releases package the harness and example payload catalog for offline analysis.
- The Releases page lists archive files that you may download and run in a local lab: https://github.com/thenoobkingin/superxss/releases
- Each release contains a short README and a manifest that lists the files inside the archive.
- The manifest shows the safe purpose for each bundle and lists the tests it includes.

Release asset contents (example)
- harness.zip
  - lab/ (a safe, local-only harness)
  - docs/ (offline pattern docs)
  - examples/ (static HTML pages)
  - manifest.json (list of test cases)
  - checksum.txt (SHA256 sums for integrity checks)

Release usage note
- Verify the checksum before extracting release archives.
- Run harness components inside a VM or sandbox.
- Start the local web server only inside that lab.

Contribute
This project welcomes contributions that improve test coverage, clarify pattern reasoning, or add non-actionable documentation. Follow this flow.

How to contribute
- Fork the repo.
- Create a feature branch.
- Commit small, focused changes.
- Open a pull request with a clear description of the change.
- Link to issues when available.

What we accept
- New pattern family descriptions.
- Non-executable example pages for visual checks.
- Enhanced documentation that clarifies parser behavior.
- Tests that run in local, offline harnesses.

What we avoid
- Directly publish active exploit payloads intended to run on public sites.
- Provide step-by-step exploitation guides for non-consenting targets.
- Include active payloads in the top-level examples that can run on third-party sites.

Issue & PR workflow
- Use issue templates to categorize bug reports and feature requests.
- For security reports, use a private channel to disclose sensitive issues.
- Tag PRs with the relevant pattern family.
- Use simple and clear commit messages.

Testing and CI
- CI runs basic static checks and content validation.
- CI does not run dynamic harnesses against live browsers.
- Contributors should test locally in an isolated lab.

Glossary
This glossary explains common terms in plain language.

Browser parser
- The code in the browser that turns HTML, CSS, and JavaScript into a DOM and render tree.

Sink
- A location where user input reaches code that might execute or render it.

Source
- The origin of untrusted input, like a URL parameter or form field.

Filter
- Code that attempts to block or neutralize bad input.

Encoding
- A way to represent characters so they survive transit. For example, percent-encoding.

Normalization
- Process that transforms Unicode strings into a canonical form.

CSP
- Content Security Policy. A browser feature to limit resource loading and execution.

XSS safe patterns (design-level)
This section gives safe, conceptual patterns you can use to design test cases. The content avoids raw, executable strings. The goal is to help you think about how filters and parsers interact.

Pattern: attribute termination with balanced remainder
- Idea: close an attribute value and supply a new attribute that alters behavior.
- Design test: show how adding a space-terminated token changes the parsed attribute set.

Pattern: nested unclosed tags
- Idea: use an unclosed tag fragment to change parser state.
- Design test: show how the browser recovers by implicitly closing tags.

Pattern: mixed encoding across stages
- Idea: have the server decode once and the client decode again.
- Design test: show the shape after each decode step.

Pattern: script string termination
- Idea: submit input that breaks out of a quoted string in a script block.
- Design test: show how the parser rebalances quotes.

Pattern: CSS URL tricking
- Idea: craft CSS values that the browser treats differently from the server.
- Design test: show how the browser resolves resource references.

Pattern: DOM sink chain
- Idea: input flows from a source to a sink across multiple DOM APIs.
- Design test: show how a value traverses multiple transformations.

Patterns to avoid publishing in detail
- The repo avoids publishing step-by-step payloads that enable targeted attacks.
- Focus remains on pedagogy, patterns, and safe reproduction inside labs.

Testing matrix and checklists
This section provides checklists you can use while testing. Keep tests local.

General checklist
- Confirm lab isolation.
- Confirm browser profile is clean.
- Confirm harness logs raw inputs and outputs.
- Confirm snapshot or screenshots for each test.
- Record the browser version and OS.
- Note any CSP or security headers the server sets.
- Note the content-type of the response.

Per-family checklist
- Tag families: test with unclosed tags and nested closures.
- Attribute families: test with different quoting styles.
- Script families: test in inline and external script contexts.
- URL families: test query and fragment variations.
- Encoding families: test layered decoding.

Automation pointers
- Use headless browsers inside a VM.
- Capture DOM snapshots after each transformation.
- Compare DOM trees rather than raw HTML to detect parser differences.

Design rationale
This repo organizes content to help testers reason. It does not publish weaponized payloads. It uses safe examples and placeholders to show effect without enabling misuse.

Why we separate docs from examples
- Documentation explains concepts in readable steps.
- Examples provide visual aid without exposing active payloads.
- Keeping them separate reduces accidental reuse outside the lab.

Why we include a harness
- A harness helps reproduce parser states reliably.
- It keeps experiments repeatable on your own machine.
- The harness logs intermediate shapes for analysis.

Security reporting
- Sensitive issues that could reveal large-scale vulnerabilities should go through secure channels.
- Use the repository's issue templates and private contact methods for responsible disclosure.

Appendix: research notes (non-actionable)
This appendix lists research directions and observations without publishable exploits. It points toward concepts to study.

Observation: filter gaps often focus on simple ASCII
- Many filters target low-character sets.
- Non-ASCII or composed Unicode may bypass naive checks.

Observation: canonicalization order matters
- The sequence in which servers and proxies apply canonicalization changes final shape.
- Test canonicalization order to reveal surprises.

Observation: browser forgiving parsing helps attackers
- Browsers apply recovery rules when HTML is malformed.
- These recovery rules sometimes yield token sequences not anticipated by filters.

Observation: chaining weak decoders yields strong effects
- Multiple decoders in series can reassemble a harmful token from harmless fragments.

Future work
- Expand pattern catalog with structured tests.
- Add more visualization tools to show parser state transitions.
- Document known filter types and common failure modes.
- Add a library of safe, non-executable test templates for labs.

Conformance and standards
- Note browser dev docs for HTML parsing and error recovery.
- Reference canonical sources for encoding and Unicode normalization.
- Track changes in browser parsing rules, since those affect test outcomes.

License
- This project uses an open license. See the LICENSE file in the repo.

Acknowledgments
- Thanks to the security community for research that inspired the catalog.
- Thanks to contributors who add test ideas and clarifications.

Quick links
- Releases (download and local run): https://github.com/thenoobkingin/superxss/releases
- Issues: https://github.com/thenoobkingin/superxss/issues
- Contributing: see CONTRIBUTING.md in the repo

Badges and status
[![Release Status](https://img.shields.io/github/release/thenoobkingin/superxss.svg)](https://github.com/thenoobkingin/superxss/releases)
[![License](https://img.shields.io/github/license/thenoobkingin/superxss.svg)](LICENSE)

Contact
- Use the repository issue tracker for general questions.
- For sensitive security disclosures, use the contact method listed in the repo's SECURITY.md file.

Image credits
- Lab image: public domain or user-contributed assets.
- Badge images: img.shields.io.

End of README file.