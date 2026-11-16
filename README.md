# ui-lab

Homework of ITDS362, Automated UI Testing. Test case QP-01 (a custom pizza reaches the admin list) on https://quickpizza.grafana.com is automated with two tools.

Tool parts that I chose: **Part 3 Robot Framework** and **Part 4 Cypress**.

## robot/

Robot Framework tests with SeleniumLibrary. `first.robot` and `web_form.robot` test Selenium's web form, `pizza_scenario.robot` is QP-01, `resources/quickpizza.resource` holds its locators and keywords, and `results/run1` and `results/run2` hold the report, log and pictures of two full runs.

Needs `CHROMEDRIVER` set to the path of ChromeDriver. Run from the `robot` folder with the virtual environment active:

```
robot -d results/run1 -v BROWSER:headlesschrome pizza_scenario.robot
```

## cypress/

Cypress specs. `cypress/e2e/web-form.cy.js` tests the web form, `cypress/e2e/pizza-scenario.cy.js` is QP-01, `cypress/support/commands.js` holds the custom command `adminLogin`, and `cypress/videos` and `cypress/screenshots` hold the evidence of a full run.

Run `npm install` once, then from the `cypress` folder:

```
npx cypress run --browser chrome
```
