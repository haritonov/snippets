- on one of the healthcare care project, there are 3 or 4 different devices for getting patient test results (weight, blood pressure, heart rate, etc.).  The previous implementation stores test results in different models (test_result, weight_result, upload_result) with different value props storing. 
These results belong to the Patient model which has access to the test results through the polymorphic relation. 

We want to get all Patient results in the same formats by:
```
> @paritent.measurements.measurements_json
"[
  {label: Weight, value: 25, unit: 'lbs', date: '20-05-2018'},
  {label: Weight, value: 30, unit: 'lbs', date: '22-07-2018'},
  {label: Heart Rate, value: 75, unit: 'bpm', date: '22-07-2018'},
]"
```

*So as a solution we did this:*
- make polymorphic relation between Patient and measurements (test_result, weight_result, upload_result)
- create a separate module `extend ActiveSupport::Concern` for extending all result models with:
    1. `"ClassMethods" "measurement_type"` for specifying JSON structure patterns;
     2. `measurements_json` method of instance for getting data
- `include IsMeasurement` in all result models
- specify JSON structure in all result models, e.g: 
```
measurement_type :weight, label: "Weight", unit: "lbs" do |weight_result|
    { value: weight_result.weight&.ceil, timestamp: weight_result.actual_time }
  end
```
The End :).