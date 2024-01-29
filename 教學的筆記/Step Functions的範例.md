# StepFunctions設定的
```json
{
  "Comment": "For test step function",
  "StartAt": "Test Case",
  "States": {
    "Test Case": {
      "Type": "Task",
      "Resource": "你的lambda arn",
      "Next": "Is Case Resolved"
    },
    "Is Case Resolved": {
      "Type": "Choice",
      "Choices": [
        {
          "Variable": "$.Status",
          "NumericEquals": 1,
          "Next": "True Case"
        },
        {
          "Variable": "$.Status",
          "NumericEquals": 0,
          "Next": "False Case"
        }
      ]
    },
    "True Case": {
      "Type": "Task",
      "Resource": "a你的lambda arn",
      "End": true
    },
    "False Case": {
      "Type": "Task",
      "Resource": "你的lambda arn",
      "Next": "Fail"
    },
    "Fail": {
      "Type": "Fail",
      "Cause": "錯了啦"
    }
  }
}
```

基本上就是用拖拉的，如果搞不定拖拉的，就用json吧
