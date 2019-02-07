# testing-toolbox-go

This library contains tools used to create unit tests and integration tests. Some tests involve modifying external state, for example, an integration test that involves creating tables and inserting rows in a PostgreSQL database. If the state change happens using a client obtained through the toolbox, the state change will be reversed when the test completes as long as the `Clear()` method is called at the end of the test.

Example:

```go
func Test_writeFoodsToDB(t *testing.T) {
    ut := testingtools.UnitToolbox("Test_writeToDB")
    it := testingtools.IntegrationToolbox(ut)
    defer it.Clear()

    it.CreatePGTable("foods", `
        CREATE TABLE public.foods
        (
            id bigserial NOT NULL,
            food_name test NOT NULL,
            PRIMARY KEY (id)
        )
        WITH (
            OIDS = FALSE
        );
    `)
    
    err := writeFoodsToDB()

    if err != nil {
        t.Error("Test failed because error returned.")
    }
}
```