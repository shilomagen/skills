# Client verification baseline

Verify in a real browser against the running app: start it the way the repo says to, drive it with whatever browser automation is available, and capture evidence as you go. Screenshots are the currency of this file — every claim about the UI is a claim about pixels, so it carries one.

## Correctness and design

- Walk every screen and state the change touches: default, loading, empty, error, long-content. Screenshot each.
- With a Figma reference: compare side by side at the same viewport — spacing, type scale, colors, radii, iconography. Pixel-perfect is the bar; "close" gets a diff note and a fix.
- Without one: judge against the app's own design system — the change should be indistinguishable in craft from the screens around it.

## UX pass

- Interaction states: hover, focus, disabled, keyboard navigation.
- Responsive: the breakpoints the app supports, plus one narrow viewport.
- Latency honesty: the UI shows progress during real waits and recovers visibly on failure.

## Performance

- Re-renders: profile the changed screens (React DevTools profiler or the framework's equivalent). An interaction should re-render the components it changes, not the tree.
- If the repo is React, run react-doctor — or the repo's own audit tooling — on the touched areas and act on what it flags.
- Bundle: compare build output size before and after; growth beyond what the feature plausibly carries needs an explanation or a fix.

## Console

Zero new errors or warnings across the walked screens. Existing noise isn't yours to fix; new noise is.
