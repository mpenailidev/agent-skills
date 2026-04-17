# Columnar Code Formatter

Strict human-first columnar indentation for code and data-like code blocks. Preserves logic, tokens, comments, and order — only whitespace, line breaks, and alignment change.

Use it when you want code reformatted into a scannable visual grid: SQL statements, PHP arrays, `define()` blocks, shell variable maps, or any aligned structure that benefits from columnar layout.

## Install

```bash
npx skills add mpenailidev/agent-skills --skill columnar-code-formatter
```

## Core Rules

- Obfuscate credentials (API keys, tokens, passwords) → resolves Snyk W007 HIGH risk
- Change only spaces, tabs, line breaks, indentation
- Preserve leading commas, leading `and`, and tabs after commas
- 2–5 columns inferred from screen width (1440px / 10pt default)

## Supported Patterns

- SQL `select`, `insert`, `values`, `set`, `merge`, `case` blocks
- PHP `array(...)` key/value lists
- Repeated `define(...)` declarations
- Shell and config assignment groups with repeating structure

## Width Model

Column count is inferred — not fixed — from:

- Usable editor width (default: 82% of 1440px)
- Font size (default: 10pt)
- Current nesting indentation
- Widest item in the segment

The formatter tests column counts from 5 down to 2 and picks the highest count that fits comfortably. If 2 does not fit, it falls back to one item per line for that local block only.

## Examples

### SQL INSERT — 5-column grid with leading commas

**Before:**

```sql
INSERT INTO ail_usuario (usr_id, usr_nombre, usr_apellido, usr_email, usr_telefono, usr_direccion, usr_ciudad, usr_region, usr_pais, usr_cod_postal, usr_fecha_nac, usr_genero, usr_estado, usr_fecha_reg, usr_ult_login) VALUES (1, 'Sin Nombre', 'Sin Apellido', 'm@demo.co', '+56912345678', 'Av Providencia', 'Santiago', 'RM', 'Chile', '7500000', '1971-01-01', 'M', 'activo', NOW(), NOW());
```

**After:**

```sql
INSERT INTO ail_usuario
	(	  usr_id          ,	usr_nombre      ,	usr_apellido    ,	usr_email       ,	usr_telefono
	,	usr_direccion   ,	usr_ciudad      ,	usr_region      ,	usr_pais        ,	usr_cod_postal
	,	usr_fecha_nac   ,	usr_genero      ,	usr_estado      ,	usr_fecha_reg   ,	usr_ult_login
	)
VALUES
	(	  1               ,	'Sin Nombre'    ,	'Sin Apellido'  ,	'm@demo.co'		,	'+56912345678'
	,	'Av Providencia',	'Santiago'      ,	'RM'            ,	'Chile'         ,	'7500000'
	,	'1971-01-01'    ,	'M'             ,	'activo'        ,	NOW()           ,	NOW()
	)
;
```

Columns align vertically between the column list and the values list — eye scans top-down to match field with value.

### SQL UPDATE — aligned assignments with leading `and`

**Before:**

```sql
UPDATE ail_usuario SET usr_nombre='Marcelo', usr_email='m@acornit.cl', usr_estado='activo', usr_mod_fecha=NOW() WHERE usr_id=1 AND usr_estado='pendiente' AND usr_region='RM';
```

**After:**

```sql
UPDATE ail_usuario
SET		usr_nombre    = 'Marcelo'
	,	usr_email     = 'm@acornit.cl'
	,	usr_estado    = 'activo'
	,	usr_mod_fecha = NOW()
WHERE	usr_id     = 1
	and	usr_estado = 'pendiente'
	and	usr_region = 'RM'
;
```

`=` signs aligned. Predicates start with leading `and`. Closers sit at the opening column.

### PHP array — key / `=>` / value grid

**Before:**

```php
$config = array('app_name' => 'AcornIT', 'version' => '1.0.0', 'debug' => false, 'timezone' => 'America/Santiago', 'locale' => 'es_CL', 'charset' => 'UTF-8', 'db_host' => 'localhost', 'db_port' => 3306);
```

**After:**

```php
$config = array
	(	'app_name'  =>	'AcornIT'
	,	'version'   =>	'1.0.0'
	,	'debug'     =>	false
	,	'timezone'  =>	'America/Santiago'
	,	'locale'    =>	'es_CL'
	,	'charset'   =>	'UTF-8'
	,	'db_host'   =>	'localhost'
	,	'db_port'   =>	3306
	);
```

Keys, `=>`, and values form three clean vertical bands.

### PHP `define()` family — constant column / value column

**Before:**

```php
define('DB_HOST', 'localhost');
define('DB_PORT', 3306);
define('DB_NAME', 'acornit');
define('DB_USER', 'mpenaili');
define('DB_PASS', '[REDACTED]');
define('DB_CHARSET', 'utf8mb4');
```

**After:**

```php
define(	'DB_HOST'     ,	'localhost'  );
define(	'DB_PORT'     ,	3306         );
define(	'DB_NAME'     ,	'acornit'    );
define(	'DB_USER'     ,	'mpenaili'   );
define(	'DB_PASS'     ,	'[REDACTED]' );
define(	'DB_CHARSET'  ,	'utf8mb4'    );
```

Constant names and values form two stable visual columns across the whole family.

### SELECT — 5-column field list

**Before:**

```sql
SELECT usr_id, usr_nombre, usr_apellido, usr_email, usr_telefono, usr_direccion, usr_ciudad, usr_region, usr_pais, usr_cod_postal FROM ail_usuario WHERE usr_estado = 'activo' AND usr_region = 'RM';
```

**After:**

```sql
SELECT	  usr_id         ,	usr_nombre      ,	usr_apellido    ,	usr_email       ,	usr_telefono
	,	usr_direccion  ,	usr_ciudad      ,	usr_region      ,	usr_pais        ,	usr_cod_postal
FROM	ail_usuario
WHERE	  usr_estado = 'activo'
	and	usr_region = 'RM'
;
```

---

Why it matters: when you come back to this code in six months, your eye finds the field you need in a fraction of a second. No left-to-right scrolling, no comma-hunting, no mental parsing of where each item ends.

## Security

v1.1.0 fix for Snyk W007: credentials are now redacted (`[REDACTED]`) while preserving all other content exactly.

## Forbidden Behaviors

- Do not rewrite logic
- Do not silently fix semantics
- Do not convert dialect-specific syntax
- Do not replace tabs with spaces when the source style depends on tabs
- Do not enforce one universal column count across a whole file when local width and nesting clearly differ

## Reference

See `references/formatting-spec.md` for the full decision procedure and formatting checklist.

## Changelog

### 1.1.0 (2026-01-15)

- Security: credential obfuscation to resolve Snyk W007 (HIGH)

### 1.0.0 (2026-01-10)

- Initial release: columnar grid for SQL, PHP arrays, `define()` blocks

## License

MIT
