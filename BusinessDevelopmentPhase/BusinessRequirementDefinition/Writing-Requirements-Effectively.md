# Writing Requirements Effectively

> [!note]  
> ![BY NC ND](../../img/Cc-by-nc-sa.png)  
> Writing Requirements Effectively © 2026 by Jen Yuan Pan is licensed under [Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.en).  


## Overview

The heart of the **Agile mindset** is simple: **do the right things, and do things right**.

At this stage, focus on defining requirements at the **business level**—avoid technical details.  
All **Feature Requirements** should include **Specification by Example** scenario tables. These help create clear, testable acceptance criteria align with BDD principle.  
> [!note]   
> BDD (Behavior-Driven Development) is mainly used for acceptance tests.

## Prerequisites
For more background, see:
- [Agile and Software Engineering](https://github.com/retamotome/SelfDirectedTraining/blob/main/AgileSoftwareEngineering/2025-11-09_AgileProjectMgt/README.md)
- [Business Requirement Definition](https://github.com/retamotome/SelfDirectedTraining/blob/main/AgileSoftwareEngineering/2025-12-08_BusinessReqDef/README.md)

Recommended books (available via BPM):
- [Specification by Example 中文版：團隊如何交付正確的軟體](https://www.tenlong.com.tw/products/9789862019481)
- [領域驅動設計：軟體核心複雜度的解決方法](https://www.books.com.tw/products/0010821330)



## Procedure

Requirements are created following the approach in [Business Requirement Definition](https://github.com/retamotome/SelfDirectedTraining/blob/main/AgileSoftwareEngineering/2025-12-08_BusinessReqDef/README.md). Each requirement should include tables (see the [requirement template](../../templates/Feature-Requirement-Template.txt)) which be presented using the method from [Specification by Example](https://www.tenlong.com.tw/products/9789862019481):

- **Risk and Solution**
- **Normal Case**
- **Error Handling**

These tables are then used to generate **acceptance** test cases in a **pseudocode-like** format, such as [Gherkin](https://cucumber.io/docs/gherkin/). Today, these can be run directly as tests using supported frameworks. This approach predates the term *“Vibe Coding”*.

In the **Identification** phase of [Business Requirement Definition](https://github.com/retamotome/SelfDirectedTraining/blob/main/AgileSoftwareEngineering/2025-12-08_BusinessReqDef/README.md), use **5W1H** (Who, What, When, Where, Why, How) to clarify each requirement table. These are then expressed in pseudocode-like syntax, as shown below, to serve as corresponding **acceptance** test cases for the requirement.

### Pseudocode-Like Syntax Example (Gherkin)
```
Feature: Brief description of what is needed
   In order to achieve a business value
   As a system actor
   I want to gain a beneficial outcome

   Scenario: A specific business situation
      Given some precondition
         And another precondition
      When an action occurs
         And another action
      Then a testable outcome is achieved
         And another result can be checked

   Scenario: Another situation
      ...
```
  
These acceptance test cases can be executed:   
* using supported frameworks,   
* integrated into CI/CD pipelines, or   
* automated with code-then-run AI agents.  


> [!note]  
> While Gherkin is used as an example here, you are not restricted to any specific language or syntax. Use whatever format is clear and effective for your team.  
> Likewise, when generating code for tests, you may use C++, Python, or any language you prefer.

---

**Learn More:**
- [如何寫出好的語法展示你的 Specification By Examples](https://ithelp.ithome.com.tw/articles/10226615)
- [Behat Gherkin Execution Example](https://docs.behat.org/en/v2.5/guides/1.gherkin.html)


