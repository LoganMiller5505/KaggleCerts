Pipelines are a way to keep preprocessing and modeling code organizing. A pipeline bundles preprocessing and modeling steps so that you can use the whole bundle as if it were a singe step. This has important benefits, including:
1. **Cleaner Code**: No need to manually keep track of training and validation data at each step.
2. **Fewer Bugs**: Fewer opportunities to missaply or forget a preprocessing step.
3. **Easier to Productionize**: Pipelines can help scale a model.
4. **More Options for Model Validation**: Includes cross-validation.

---
These three steps construct a full pipeline:
1. **Define Preprocessing Steps**
    The `ColumnTransformer` class bundles together different preprocessing steps.
    
    Example: Impute missing values in numerical data as well as imputing missing values and applying a one-hot encoding to categorical data.
    ```python
    from sklearn.pipeline import Pipeline
    from sklearn.impute import SimpleImputer
    from sklearn.preprocessing import OneHotEncoder

    numerical_transformer = SimpleImputer(strategy='constant')
    categorical_transformer = Pipeline(steps=[
        ('imputer', SimpleImputer(strategy='most_frequent')),
        ('onehot', OneHotEncoder(handle_unknown='ignore'))
    ])

    preprocessor = ColumnTransformer(
        transformers=[
            ('num', numerical_transformer, numerical_cols),
            ('cat', categorical_transformer, categorical_cols)
        ])
    ```
2. **Define the Model**
    Example: Simple RandomForestRegressor, used before.
    ```python
    from sklearn.ensemble import RandomForestRegressor

    model = RandomForestRegressor(n_estimators=100, random_state=0)
    ```
3. **Create and Evaluate the Pipeline**
    With the pipeline, preprocessing the data and fitting the model is done in a single line of code.
    Example: The unprocessed features in `X_valid` are supplied to the `predict()` command, and the pipeline automatically preprocesses the features before generating predictions.
    ```python
    from sklearn.metrics import mean_absolute_error

    my_pipeline = Pipeline(steps=[('preprocessor', preprocessor),
                                ('model', model)
                                ])

    my_pipeline.fit(X_train, y_train)

    preds = my_pipeline.predict(X_valid)

    score = mean_absolute_error(y_valid, preds)
    print('MAE:', score)
    ```