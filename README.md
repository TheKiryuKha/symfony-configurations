# symfony-configurations
## Quick installation
```
"install:dev": [
  "composer require --dev friendsofphp/php-cs-fixer",
  "composer require --dev phpstan/phpstan-symfony",
  "composer require --dev rector/rector"
]
```

## PHP-CS-FIXER
```
<?php

$finder = (new PhpCsFixer\Finder())
    ->in(__DIR__)
    ->exclude(['var', 'vendor', 'node_modules', 'tmp'])
;

return (new PhpCsFixer\Config())
    ->setRiskyAllowed(true)
    ->setFinder($finder) 
    ->setRules ([ 
        '@PSR12' => true,
        '@Symfony' => true,
        
        'array_syntax' => ['syntax' => 'short'],
        'backtick_to_shell_exec' => true,
        'date_time_immutable' => true,
        'declare_strict_types' => true,
        'lowercase_keywords' => true,
        'lowercase_static_reference' => true,
        'final_class' => true,
        'final_internal_class' => true,
        'final_public_method_for_abstract_class' => true,
        'fully_qualified_strict_types' => true,
        'global_namespace_import' => [
            'import_classes' => true,
            'import_constants' => true,
            'import_functions' => true,
        ],
        'mb_str_functions' => true,
        'modernize_types_casting' => true,
        'new_with_parentheses' => false,
        'no_superfluous_elseif' => true,
        'no_useless_else' => true,
        'no_multiple_statements_per_line' => true,
        'ordered_class_elements' => [
            'order' => [
                'use_trait',
                'case',
                'constant',
                'constant_public',
                'constant_protected',
                'constant_private',
                'property_public',
                'property_protected',
                'property_private',
                'construct',
                'destruct',
                'magic',
                'phpunit',
                'method_abstract',
                'method_public_static',
                'method_public',
                'method_protected_static',
                'method_protected',
                'method_private_static',
                'method_private',
            ],
            'sort_algorithm' => 'none',
        ],
        'ordered_interfaces' => true,
        'ordered_traits' => true,
        'protected_to_private' => true,
        'self_accessor' => true,
        'self_static_accessor' => true,
        'strict_comparison' => true,
        'visibility_required' => [
            'elements' => ['const', 'method', 'property'],
        ],
        'blank_line_after_opening_tag' => true,
        'braces' => [
            'allow_single_line_closure' => true,
            'position_after_functions_and_oop_constructs' => 'next',
        ],
        'concat_space' => ['spacing' => 'one'],
        'no_unused_imports' => true,
        'phpdoc_order' => true,
        'single_quote' => true,
        'trailing_comma_in_multiline' => true,
        'yoda_style' => [
            'equal' => false,
            'identical' => false,
            'less_and_greater' => false,
        ],
        'class_attributes_separation' => true,
        'no_blank_lines_after_class_opening' => true,
        'no_empty_phpdoc' => true,
        'no_superfluous_phpdoc_tags' => ['allow_mixed' => true],
        'phpdoc_align' => ['align' => 'left'],
        'phpdoc_summary' => true,
        'return_type_declaration' => ['space_before' => 'none'],
        'single_line_throw' => false, 
    ])
    ->setUsingCache(true);
```

## PHPSTAN
```
parameters:
    paths:
        - src
        - config
        - migrations

    level: max

    excludePaths:
        analyse:
            - vendor
    
    ignoreErrors:
        - '#Property .+::\$id \(int\|null\) is never assigned int so it can be removed from the property type\.$#'
```

## Rector
```
<?php

declare(strict_types=1);

use Rector\Caching\ValueObject\Storage\FileCacheStorage;
use Rector\Config\RectorConfig;
use Rector\Doctrine\Set\DoctrineSetList;
use Rector\Php83\Rector\ClassMethod\AddOverrideAttributeToOverriddenMethodsRector;
use Rector\Set\ValueObject\SetList;
use Rector\Symfony\Set\SymfonySetList;

return RectorConfig::configure()
    ->withSets([
        // SymfonySetList::SYMFONY_60,
        // SymfonySetList::SYMFONY_61,
        // SymfonySetList::SYMFONY_62,
        // SymfonySetList::SYMFONY_63,
        // SymfonySetList::SYMFONY_64,
        SymfonySetList::SYMFONY_70,

        SymfonySetList::SYMFONY_CODE_QUALITY,
        SymfonySetList::SYMFONY_CONSTRUCTOR_INJECTION,
        SymfonySetList::ANNOTATIONS_TO_ATTRIBUTES,

        DoctrineSetList::DOCTRINE_CODE_QUALITY,
        DoctrineSetList::DOCTRINE_DBAL_30,
        DoctrineSetList::DOCTRINE_ORM_214,
        DoctrineSetList::DOCTRINE_ORM_25,
        DoctrineSetList::ANNOTATIONS_TO_ATTRIBUTES,

        SetList::CODE_QUALITY,
        SetList::DEAD_CODE,
        SetList::TYPE_DECLARATION,
    ])
    ->withImportNames(
        removeUnusedImports: true,
    )
    ->withCache(
        cacheDirectory: '/tmp/rector',
        cacheClass: FileCacheStorage::class,
    )
    ->withPaths([
        __DIR__ . '/src',
        __DIR__ . '/config',
        __DIR__ . '/migrations',
        __DIR__ . '/tests',
        __DIR__ . '/public/index.php',
    ])
    ->withSkip([
        AddOverrideAttributeToOverriddenMethodsRector::class,
        __DIR__ . '/vendor',
        __DIR__ . '/var',
        __DIR__ . '/translations',
        __DIR__ . '/assets',
    ])
    ->withPreparedSets(
        deadCode: true,
        codeQuality: true,
        typeDeclarations: true,
        privatization: true,
        earlyReturn: true
    )
    ->withPhpSets();

```


## Conposer scripts
```
"lint": "php-cs-fixer fix",
"refactor": "rector",
"fix": [
    "@refactor",
    "@lint"
],
"test:lint": [
    "php-cs-fixer fix --dry-run",
    "rector --dry-run"
],
"test:types": "phpstan analyse",
"test:unit": "phpunit",
"test": [
    "@test:lint",
    "@test:unit",
    "@test:types"
]
```
