Azure Data Studio is a modified version of VS Code, so many of the UX Element are shared. You can see a list of these components and their usage [here](https://code.visualstudio.com/api/ux-guidelines/overview).

Along with that, we have compiled a list of guidelines that we follow in Azure Data Studio. If you are developing an extension then most of these will be handled for you, but it's still a good idea to keep them in mind during development and testing to verify that experiences are the same across Azure Data Studio.

1. Use existing components if possible. If specialized functionality is required, it's better to build on top of existing components instead of writing an entirely new one from scratch.
   * Existing components support theming and accessibility
   * Fixes only need to be made in one place
   * Using the same components ensures a consistent experience throughout Azure Data Studio
2. If a new feature requires a new component that is generic and can be reused many places, it should be put under [src/sql/base/browser/ui](https://github.com/microsoft/azuredatastudio/tree/main/src/sql/base/browser/ui). If needed by an extension, this will require additional work to hook it into the Extensibility API as well.
3. Any UI component that can be clicked should also support "Enter" and/or "Space" as well to trigger the action.
4. Scope CSS to that feature component, so that it won't overwrite other areas.
5. If your component is modal, pressing tab should only circle through components in the dialog. When the modal is closed, the focus to go back to the last focusable item before the modal was opened. ESC keyboard should close the modal.

When submitting PRs that contain UI changes, verify the followings before submitting:
1. Make sure you can navigate through all UI components using tabs/key binding shortcuts
2. Make sure you can perform the task using only keyboard navigation
3. Make sure theming works. Check at least 3 themes: light, dark, and high contrast
   * The colors should come from the theme, not CSS file