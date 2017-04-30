# Grammar of Postgre SQL

```
repeat<T> ::= T ("," T)*

query ::= select | values

select ::= TABLE ONLY? table_name "*"?
	|	withClause?
		SELECT
			(	ALL | DISTINCT ( ON "(" repeat<expression> ")" )? 		)?
			(	repeat< * | expression (AS? output_name)? >				)?
			(	FROM repeat<from_item>									)?
			(	WHERE condition											)?
			(	GROUP BY repeat<grouping_element>						)?
			(	HAVING repeat<condition>								)?
			(	WINDOW repeat<window_name AS "(" window_definition ")">	)?
			(	(UNION | INTERSECT | EXCEPT) (ALL | DISTINCT)? (query)	)?
			(	ORDER BY repeat<expression (ASC | DESC | USING operator)? (NULLS (FIRST | LAST))?>	)?
			(	LIMIT (count | ALL)										)?
			(	OFFSET start (ROW | ROWS)?								)?
			(	FETCH (FIRST | NEXT) count? (ROW | ROWS) ONLY			)?
			(	FOR ((UPDATE | NO KEY UPDATE | SHARE | KEY SHARE) (OF repeat<table_name>)? (NOWAIT | SKIP LOCKED)?)+	)?

from_item ::= table_query | sub_query | with_query_ref | joined_query

table_query		::= ONLY? table_name "*"? asAlias? ( TABLESAMPLE sampling_method "(" repeat<argument> ")" (REPEATABLE "(" seed ")")? )? 
sub_query		::= LATERAL? "(" query ")" asAlias
with_query_ref	::= with_query_name asAlias?
function_call_query ::=
		LATERAL? functionCall (WITH ORDINALITY)? asAlias?
	|	LATERAL? functionCall (AS? alias | AS) columnDefinitions
	|	LATERAL? ROWS FROM "(" repeat<functionCall ( AS columnDefinitions )?> ")" (WITH ORDINALITY)? ( AS? alias (columnDefinitions)? )?
joined_query 	::=	from_item NATURAL? join_type from_item ( ON join_condition | USING "(" repeat<join_column> ")" )?

join_type ::= (INNER? | (LEFT | RIGHT | FULL) OUTER? | CROSS) JOIN

functionCall ::= function_name "(" repeat<argument>? ")"
asAlias ::= AS? alias ( "(" repeat<column_alias> ")" )?
columnDefinitions ::= "(" repeat<column_definition> ")"

grouping_element ::=
		"(" ")" | expressionOrList
	| ROLLUP "(" repeat<expressionOrList> ")"
	| CUBE "(" repeat<expressionOrList> ")"
	| GROUPING SETS "(" repeat<grouping_element> ")"

expressionOrList ::= expression | "(" repeat<expression> ")"

withClause ::= (WITH RECURSIVE? repeat<with_query>)
with_query ::= with_query_name ("(" repeat<column_alias> ")")? AS "(" (query | insert | update | delete) ")"
with_query_name ::= name
column_alias ::= name



values ::= VALUES repeat<"(" repeat<expression | DEFAULT> ")">
	(ORDER BY repeat<expression (ASC | DESC | USING operator)?> )?
	(LIMIT (count | ALL))?
	(OFFSET start)?
	(FETCH (FIRST | NEXT) count? (ROW | ROWS) ONLY)?



insert ::= 
		withClause?
		INSERT INTO table_name (AS alias)? ("(" repeat<column_name> ")")?
			( DEFAULT VALUES | query )
			( ON CONFLICT conflict_target? conflict_action )?
			returningClause?

returningClause ::= RETURNING repeat<* | output_expression (AS? output_name)?>

conflict_target ::=
	"(" repeat<( index_column_name | "(" index_expression ")" )? ( COLLATE collation )? ( opclass )?> ")" ( WHERE index_predicate )?
	| ON CONSTRAINT constraint_name

conflict_action ::=
	DO NOTHING
	| DO UPDATE SET repeat<
					column_name "=" (expression | DEFAULT) |
					"(" repeat<column_name> ")" = "(" repeat<{ expression | DEFAULT }> ")" |
					"(" repeat<column_name> ")" = "(" query ")"
				> (WHERE condition)?


update ::= 
	withClause?
	UPDATE ONLY? table_name "*"? ( AS? alias )?
		SET repeat<
			column_name "=" (expression | DEFAULT) |
			"(" repeat<column_name> ")" = "(" repeat<{ expression | DEFAULT }> ")" |
			"(" repeat<column_name> ")" = "(" query ")"
		>
		(FROM repeat<from_item>)?
		(WHERE condition | WHERE CURRENT OF cursor_name)?
		returningClause?

delete ::= 
	withClause?
	DELETE FROM ONLY? table_name "*"? ( AS? alias )?
		(USING repeat<from_item>)?
		(WHERE condition | WHERE CURRENT OF cursor_name)?
		returningClause?

```
