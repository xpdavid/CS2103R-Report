## Additional Static Analysis Rules for Teammates (Java)

### Introduction

#### Background

Currently, five(six) static analysis tools are used in TEAMMATES.

Here are the configuration of them.

- [checkstyle](https://github.com/TEAMMATES/teammates/blob/bd97f4210749b8a58a8285258098c2f91d492099/static-analysis/teammates-checkstyle.xml)
- [eslint](https://github.com/TEAMMATES/teammates/blob/bd97f4210749b8a58a8285258098c2f91d492099/static-analysis/teammates-eslint.yml)
- [macker](https://github.com/TEAMMATES/teammates/blob/bd97f4210749b8a58a8285258098c2f91d492099/static-analysis/teammates-macker.xml)
- [pmd](https://github.com/TEAMMATES/teammates/blob/bd97f4210749b8a58a8285258098c2f91d492099/static-analysis/teammates-pmd.xml) (As we decide some of rules in PMD will only be enforced in production, we write another [configuration](https://github.com/TEAMMATES/teammates/blob/bd97f4210749b8a58a8285258098c2f91d492099/static-analysis/teammates-pmdMain.xml) in addition to this)
- [stylint](https://github.com/TEAMMATES/teammates/blob/bd97f4210749b8a58a8285258098c2f91d492099/static-analysis/teammates-stylelint.yml)
- [findbugs](https://github.com/TEAMMATES/teammates/blob/bd97f4210749b8a58a8285258098c2f91d492099/build.gradle#L326) (Only one pattern `FindDeadLocalStores` is used)

*Updated at commit [bd97f42](https://github.com/TEAMMATES/teammates/commit/bd97f4210749b8a58a8285258098c2f91d492099) at master* 

In discussion in [#6519](https://github.com/TEAMMATES/teammates/issues/6519), we agree that we use checkstyle for **style** issues and PMD for **coding/design** issues. The rest of the report will follow the rule also.

`macker` is used to detect violation in high-level design principle. For example, one of the current enforced rule says "Test cases should not be dependent on each other". Currently, there are only two rules for this analyser. More rules should be added to reflect the [design](https://github.com/TEAMMATES/teammates/blob/90b40c0e18c5856424178b0fb99964c4a3cdb2da/docs/design.md).

`findbugs` cannot print violations in build log. We cannot check wether the build is successful or not in CI environment. Therefore, we exclude it and only put one rule for it.

### Discussion

This report will discuss the static analysis tools for java, which are `checkstyle`, `macker`, `pmd`, `findbugs`. Rules or customised checks will be from these tools.

We can divide the possible static analysis rules into three part. `Coding Standard`, `Design Principle` and `Bug Prevention`. 

#### Coding Standard

We use coding standard in [OSS Generic](https://oss-generic.github.io/process/codingStandards/CodingStandard-Java.html) project for TEAMMATES. Most of the standards have been enforced through the checkStyle configuration. Severals of them can be explored more.

##### Boolean Variable Naming Conversion

In the coding standard, it says that "boolean variables should be prefixed with ‘is’". There are a few alternatives, which are "has", "can", "should". Also it required to avoid boolean variables that represent the negation of a thing (`isNotInitialized` is better than `isInitialized `).

Currently, all the static analysis tools don't support this standard. We shall write our own check.

CheckStyle provided a way to write customised check. [Here](https://github.com/xpdavid/teammates/blob/checkstyle-boolean-variable/static-analysis/checkstyle-lib/src/java/BooleanNameCheck.java) is what the checks look like for this particular boolean naming rule.

``` java
// Part of code (Core Logic)

for (String allowedPrefix : allowedPrefixes) {
    if (variableName.startsWith(allowedPrefix)) {
        if (isNameNegationOfThing(variableName, allowedPrefix)) {
            log(ast.getLineNo(), ast.getColumnNo(),
                    String.format(MSG_NEGATION, variableName), ast.getText()); // violation caught
        }
        return;
    }
}

log(ast.getLineNo(), ast.getColumnNo(),
        String.format(MSG_NAMING, Arrays.toString(allowedPrefixes)), ast.getText()); // violation caught
```
When run the code against TEAMMATES ([bd97f42](https://github.com/TEAMMATES/teammates/commit/bd97f4210749b8a58a8285258098c2f91d492099 at master)), here are reports for `checkstyleMain` and `checkstyleTest`

- [CheckstyleMain](http://htmlpreview.github.io/?https://github.com/xpdavid/CS2103R-Report/blob/master/codingStandard/booleanNaming/main.html)
- [CheckstyleTest](http://htmlpreview.github.io/?https://github.com/xpdavid/CS2103R-Report/blob/master/codingStandard/booleanNaming/test.html)

For production code, there are 122 violations. Some of them may be reasonable as its name may start with `allow` instead of `is, has, can, should`. For the negation of naming, there are some false positive such as `isNotSureAllowed` and `isNotificationIconShown`.

##### Variable Declaration Usage Distance

##### Comments Indentation

##### Spelling of word

#### Design Principle

##### API Design

##### Abstraction Level

#### Bug Prevention

##### Findbugs Enforcement

### Analysis

### Conclusion
