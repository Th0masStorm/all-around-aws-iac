Conditions
---
- Conditions are a powerful tool to create universal and reusable templates
- Conditions are declared as a Template property and using `Fn::If`, `Fn::Equals`, `Fn::Not`, `Fn::And` and `Fn::Or`
- Conditions only have True and False as values
- When using Condition in Resource reference it must be strictly a condition name. Intrinsic functions are prohibited
- Condition element in Resource reference means resource is created *only* if the the statement is *True*
- You can also use conditions in Resource properties (check 01-PropertyWideConditions.yaml)
