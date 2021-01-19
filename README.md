# 生成 didi sql mod 脚手架

```
使用之前要更改结构体注释
```

#### gforge

`gforge` is a cli tool based on [gendry](github.com/didi/gendry), it generates golang struct according to the table name which could ease your burden. Even gforge can generate the complete DAO layer for you.

#### install

`go get -u github.com/caibirdme/gforge`

#### usage

```
> gforge -h
A collection of tools to generate code for operating database supported by Gendry

Options:

  -h, --help   display help information
  -v           version

Commands:

  help    display help information
  table   schema could generate go struct code for given table
  dao     dao generates code of dao layer by given table name
```

Get the subcommand help information

```
> gforge help table
schema could generate go struct code for given table

Options:

  -d               database name
  -t               table name
  -u               user name
  -p               password
  -h[=localhost]   host
  -P[=3306]        port
```

Generate a struct for table

```
> gforge table -uusername -ppassword -hip -dinformation_schema -tCOLUMNS

// COLUMNS is a mapping object for COLUMNS
type COLUMNS struct {
	TABLECATALOG string `json:"TABLE_CATALOG"
	TABLESCHEMA string `json:"TABLE_SCHEMA"
	TABLENAME string `json:"TABLE_NAME"
	COLUMNNAME string `json:"COLUMN_NAME"
	ORDINALPOSITION uint64 `json:"ORDINAL_POSITION"
	COLUMNDEFAULT string `json:"COLUMN_DEFAULT"
	ISNULLABLE string `json:"IS_NULLABLE"
	DATATYPE string `json:"DATA_TYPE"
	CHARACTERMAXIMUMLENGTH uint64 `json:"CHARACTER_MAXIMUM_LENGTH"
	CHARACTEROCTETLENGTH uint64 `json:"CHARACTER_OCTET_LENGTH"
	NUMERICPRECISION uint64 `json:"NUMERIC_PRECISION"
	NUMERICSCALE uint64 `json:"NUMERIC_SCALE"
	DATETIMEPRECISION uint64 `json:"DATETIME_PRECISION"
	CHARACTERSETNAME string `json:"CHARACTER_SET_NAME"
	COLLATIONNAME string `json:"COLLATION_NAME"
	COLUMNTYPE string `json:"COLUMN_TYPE"
	COLUMNKEY string `json:"COLUMN_KEY"
	EXTRA string `json:"EXTRA"
	PRIVILEGES string `json:"PRIVILEGES"
	COLUMNCOMMENT string `json:"COLUMN_COMMENT"
	GENERATIONEXPRESSION string `json:"GENERATION_EXPRESSION"
}
```

The produced struct could pass the examine of golint and govet

Generate codes of dao layer about one table

```
> gforge dao -uusername -ppassword -hip -dinformation_schema -tCOLUMNS | gofmt
package COLUMNS

import (
	"database/sql"
	"errors"
	"github.com/didichuxing/gendry/builder"
	"github.com/didichuxing/gendry/scanner"
)

/*
	This code is generated by ddtool
*/

// COLUMNS is a mapping object for COLUMNS
type COLUMNS struct {
	TABLECATALOG           string `json:"TABLE_CATALOG"`
	TABLESCHEMA            string `json:"TABLE_SCHEMA"`
	TABLENAME              string `json:"TABLE_NAME"`
	COLUMNNAME             string `json:"COLUMN_NAME"`
	ORDINALPOSITION        uint64 `json:"ORDINAL_POSITION"`
	COLUMNDEFAULT          string `json:"COLUMN_DEFAULT"`
	ISNULLABLE             string `json:"IS_NULLABLE"`
	DATATYPE               string `json:"DATA_TYPE"`
	CHARACTERMAXIMUMLENGTH uint64 `json:"CHARACTER_MAXIMUM_LENGTH"`
	CHARACTEROCTETLENGTH   uint64 `json:"CHARACTER_OCTET_LENGTH"`
	NUMERICPRECISION       uint64 `json:"NUMERIC_PRECISION"`
	NUMERICSCALE           uint64 `json:"NUMERIC_SCALE"`
	DATETIMEPRECISION      uint64 `json:"DATETIME_PRECISION"`
	CHARACTERSETNAME       string `json:"CHARACTER_SET_NAME"`
	COLLATIONNAME          string `json:"COLLATION_NAME"`
	COLUMNTYPE             string `json:"COLUMN_TYPE"`
	COLUMNKEY              string `json:"COLUMN_KEY"`
	EXTRA                  string `json:"EXTRA"`
	PRIVILEGES             string `json:"PRIVILEGES"`
	COLUMNCOMMENT          string `json:"COLUMN_COMMENT"`
	GENERATIONEXPRESSION   string `json:"GENERATION_EXPRESSION"`
}

//GetOne gets one record from table COLUMNS by condition "where"
func GetOne(db *sql.DB, where map[string]interface{}) (*COLUMNS, error) {
	if nil == db {
		return nil, errors.New("sql.DB object couldn't be nil")
	}
	cond, vals, err := builder.BuildSelect("COLUMNS", where, nil)
	if nil != err {
		return nil, err
	}
	row, err := db.Query(cond, vals...)
	if nil != err || nil == row {
		return nil, err
	}
	defer row.Close()
	var res *COLUMNS
	err = scanner.Scan(row, &res)
	return res, err
}

//GetMulti gets multiple records from table COLUMNS by condition "where"
func GetMulti(db *sql.DB, where map[string]interface{}) ([]*COLUMNS, error) {
	if nil == db {
		return nil, errors.New("sql.DB object couldn't be nil")
	}
	cond, vals, err := builder.BuildSelect("COLUMNS", where, nil)
	if nil != err {
		return nil, err
	}
	row, err := db.Query(cond, vals...)
	if nil != err || nil == row {
		return nil, err
	}
	defer row.Close()
	var res []*COLUMNS
	err = scanner.Scan(row, &res)
	return res, err
}

//Insert inserts an array of data into table COLUMNS
func Insert(db *sql.DB, data []map[string]interface{}) (int64, error) {
	if nil == db {
		return nil, errors.New("sql.DB object couldn't be nil")
	}
	cond, vals, err := builder.BuildInsert("COLUMNS", data)
	if nil != err {
		return 0, err
	}
	result, err := db.Exec(cond, vals...)
	if nil != err || nil == result {
		return 0, err
	}
	return result.LastInsertId()
}

//Update updates the table COLUMNS
func Update(db *sql.DB, where, data map[string]interface{}) (int64, error) {
	if nil == db {
		return 0, errors.New("sql.DB object couldn't be nil")
	}
	cond, vals, err := builder.BuildUpdate("COLUMNS", where, data)
	if nil != err {
		return 0, err
	}
	result, err := db.Exec(cond, vals...)
	if nil != err {
		return 0, err
	}
	return result.RowsAffected()
}

// Delete deletes matched records in COLUMNS
func Delete(db *sql.DB, where,data map[string]interface{}) (int64, error) {
	if nil == db {
		return 0, errors.New("sql.DB object couldn't be nil")
	}
	cond,vals,err := builder.BuildDelete("{{.TableName}}", where)
	if nil != err {
		return 0, err
	}
	result,err := db.Exec(cond, vals...)
	if nil != err {
		return 0, err
	}
	return result.RowsAffected()
}
```
