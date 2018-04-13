1. Use existing components if possible. 

	- Existing components support theming and accessibility. 
2. If  the new feature requires new UIs that are generic enough and can be reused many places, please create a new UI component under C:\Repro\sqlopsstudio\src\sql\base\browser.
3. When implement a click or double click function, make sure to support "Enter" keyboard shortcut
4. Scope CSS to that feature component, so that it won't overwrite other areas

### Please verify the followings before sending out the PR
1. Make sure you can navigate through all UI components using tabs/key binding shortcuts
2. Make sure you can perform a task using keyboards
3. Make sure theming works. Check at least 3 themes: light, dark, and high contrast
	- The colors should come from the theme, not CSS file