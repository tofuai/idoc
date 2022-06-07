# :exclamation: Asynchronous API Web Backend to AI Backend


```mermaid
sequenceDiagram
Web ->> AI: Asynchronous semi-annotate labeling (via RabbitMQ)
AI -->> Web: Return result (via RabbitMQ)
Web ->> AI: Asynchronous training (via RabbitMQ)
AI -->> Web: Return result (via RabbitMQ)
Web ->> AI: Asynchronous document analysis (via RabbitMQ)
AI -->> Web: Return result (via RabbitMQ)
```

## :fire:  Asynchronous Semi-annotate Labeling
After creating a new document type, we need to provide semi-annotated labels for the new training data. This process shortens human labeling time. Basically, it is the same as **Asynchronous Key Information Extraction** with AI model and post-processin rules from the base document type.

### :arrow_right: Request Structure

|Field Name|Type|Description|
|--|--|--|
|request_id| STRING | The unique identifier for the request.|
|document_type|[DocumentType](#fire-documenttype)|Document Type detail.|
|url| STRING | URL to the document need to apply automatic semi-labelling. |
|output_queue_name | STRING | Output queue name to store the return result.|

### :arrow_left: Response Structure

|Field Name|Type|Description|
|--|--|--|
|request_id|STRING|The unique identifier for the request.|
|status|STRING|The status of request, success or fail.|
|document_analysis_id|STRING|The unique identifier for the document analysis result.|

## :fire: Asynchronous Training

### :arrow_right: Request Structure

|Field Name|Type|Description|
|--|--|--|
|request_id| STRING | The unique identifier for the request.|
|document_type|[DocumentType](#fire-documenttype)|Document Type detail.|
|document_analysis_ids|An array of STRING| A list of document analysis id. |

### :arrow_left: Response Structure

|Field Name|Type|Description|
|--|--|--|
|request_id|STRING|The unique identifier for the request.|
|status|STRING|The status of request, success or fail.|
|accuracy|FLOAT|The average accuracy of model on the training data.|

## :fire: Asynchronous Document Analysis
After training successfully, the **AI Backend** will have sufficient AI models and post-processing rules for a full pipeline of intelligence document processing. 

### :arrow_right: Request Structure

|Field Name|Type|Description|
|--|--|--|
|request_id|STRING|Unique identifier for each request.|
|document_type|[DocumentType](#fire-documenttype)|Document Type detail.|
|url|STRING|S3 url to the document.|
|output_queue_name|STRING|Output queue name to store the return result.|

### :arrow_left: Response Structure

|Field Name|Type|Description|
|--|--|--|
|document|[DocumentAnalysis](#fire-document-analysis)||
|text_extraction|JSON|The raw text extracted from a document.|
|form_extraction|JSON|Form data extraction with specific fields. They are represented as key-value pair.|
|table_extraction|JSON|Table extraction with specific fields. They are seperated and matched base on column header.|
		
# :exclamation: REST API AI Backend to Web Backend

```mermaid
sequenceDiagram
AI ->> Web: Create document type
Web -->> AI: Return result
AI ->> Web: Update document type by id
Web -->> AI: Return result
AI ->> Web: Get document type by id
Web -->> AI: Return result
AI ->> Web: Delete document type by id
Web -->> AI: Return result
AI ->> Web: Save document analysis result
Web -->> AI: Return result
AI ->> Web: Get document analysis result by id
Web -->> AI: Return result
AI ->> Web: Update document analysis result by id
Web -->> AI: Return result
AI ->> Web: Delete document analysis result by id
Web -->> AI: Return result
```

## :fire: Create Document Type

|Endpoint|HTTP Method|
|--|--|
|2.0/idp/document_type/create|POST|

### :arrow_right: Request Structure

|Field Name|Type|Description|
|--|--|--|
| name | STRING | Human readable name that identifiers the document type. This name must be unique in each tenant. | description | STRING | Description for this document type. |
| base_document_type_id | STRING | The identifier of the base document type for quick set-up and automatic semi-labeling. |
|language_code|STRING|Primary [language code](http://www.lingoes.net/en/translator/langcode.htm) for this document type.|
| ocr_engine | STRING | OCR Engine to applied for this document type. |
| form_fields | An array for [FormField](formfield) | Metadata for form fields. **Note** that the form field name must be unique in each document type. |
| tables | An array for [Table](table) | Configuration for table. **Note** that the table name must be unique in each document type.) |
| ai_config | JSON | Config for **AI Backend** |


### :arrow_left: Response Structure

|Field Name|Type|Description|
|--|--|--|
| document_type | [DocumentType](#fire-documenttype) | Document Type Object.  |

## :fire: Update Document Type by id

|Endpoint|HTTP Method|
|--|--|
|2.0/idp/document_type/update|POST|

### :arrow_right: Request Structure

|Field Name|Type|Description|
|--|--|--|
| document_type_id | STRING | Unique identifier for the document type. |
| description |STRING|Description for this document type.|
| active | BOOL | Current active status of the document. If true, user in the same tenant can use this document type. |
| public | BOOL | If the document type is public, all user can apply it for their own pipeline. This option is only available for the **AI Team** to create global base model.  |
| form_fields | An array for [FormField](formfield) |  for form fields. **Note** that the form field name must be unique in each document type. |
| tables | An array for [Table](table) | Configuration for table. **Note** that the table name must be unique in each document type.) |
| ai_config | JSON | Config for **AI Backend** |


### :arrow_left: Response Structure

|Field Name|Type|Description|
|--|--|--|
| document_type | [DocumentType](#fire-documenttype) | Document Type Object.  |


## :fire: Get Document Type by id

|Endpoint|HTTP Method|
|--|--|
|2.0/idp/document_type/get|POST|

### :arrow_right: Request Structure

|Field Name|Type|Description|
|--|--|--|
| document_type_id | STRING | Unique identifier for the document type. |

### :arrow_left: Response Structure

|Field Name|Type|Description|
|--|--|--|
| document_type | [DocumentType](#fire-documenttype) | Document Type Object.  |

## :fire: Delete Document Type by id

|Endpoint|HTTP Method|
|--|--|
|2.0/idp/document_type/delete|POST|

### :arrow_right: Request Structure

|Field Name|Type|Description|
|--|--|--|
| document_type_id | STRING | Unique identifier for the document type. |


# :exclamation: Data Structure

## :fire:  DocumentType

Document type

| Field Name | Type | Description |
|--|--|--|
| document_type_id | STRING | Unique identifier for the document type. |
| tenant_id | STRING | Unique identifier for the tenant. |
| base_document_type_id | STRING | The identifier of the base document type for quick set-up and automatic semi-labeling. |
| name | STRING | Human readable name that identifiers the document type. This name must be unique in each tenant. |
| active | BOOL | Current active status of the document. If true, user in the same tenant can use this document type. |
| public | BOOL | If the document type is public, all user can apply it for their own pipeline. This option is only available for the **AI Team** to create base model.  |
| language_code | STRING | Primary [language code](http://www.lingoes.net/en/translator/langcode.htm) for this document type. |
| ocr_engine | STRING | OCR Engine to applied for this document type. |
| form_field_configs | An array for [FormField](formfield) | A list of configurations for mutiple form fields. **Note** that the form field name must be unique in each document type. |
| table_configs | An array for [Table](table) | A list of configurations for multiple tables. **Note** that the table name must be unique in each document type.) | 
| ai_config | JSON | Config for **AI Backend** |
| last_update_time | INT64 | Last update time. |
| creation_time | INT64 | Creation time. |

## :fire: FormFieldConfig

Form field

| Field Name | Type | Description |
|--|--|--|
| name | STRING | Unique identifier for the form field. |
| alias | an array of STRING | A list of alias texts for the field label. |
| data_type | STRING | Data type for validation. |
| required | BOOL | True if this field is required. |
| logic_code | STRING | Python logic code for post-processing. |

## :fire: TableConfig

Table

| Field Name |  Type | Description |
|--|--|--|
| name | STRING | Unique identifier for the table. |
| required | BOOL | True if this field is required. |
| logic_code | STRING | Python logic code for post-processing. |
| column_configs | An array for [ColumnConfig](#fire-columnconfig) | A list of configurations for multiple table columns.  **Note** that the table column name must be unique in each table. |

## :fire: ColumnConfig

Column

| Field Name | Type | Description |
|--|--|--|
| name | STRING | Unique identifier for the table column. |
| alias | an array of STRING | A list of alias texts for the column header label. |
| data_type | STRING | Data type for validation. |
| required | BOOL | True if this field is required. |
| logic_code | STRING | Python logic code for post-processing. |

## :fire: DocumentAnalysis

DocumentAnalysis

| Field Name | Type | Description |
|--|--|--|
| document_id | STRING | Unique identifier for the document. |
| document_type_id | STRING | Unique identifier for the document type. |
| version | STRING  | Document analysis model version. |
| path | STRING | Path of the original document. |
| pages | An array of [PageAnalysis](#fire-pageanalysis) | A list of page data. |
| num_page | INT64 | Number of pages in the document. |

## :fire: PageAnalysis

PageAnalysis

| Field Name | Type | Description |
|--|--|--|
| width | INT64 | Width of the page image. |
| height | INT64 | Height of the page image. |
| class | STRING | Classification result of the page. |
| text_extraction | [TextExtraction](#fire-textextraction) | text extraction result. |
| form_extraction | [FormExtraction](#fire-formextraction) | form extraction result. |
| table_extraction | [TableExtraction](#fire-tableextraction) | table extraction result. |

## :fire: TextExtraction

TextExtraction

| Field Name | Type | Description |
|--|--|--|
|  |  |  |

## :fire: FormExtraction

FormExtraction

| Field Name | Type | Description |
|--|--|--|
|  |  |  |

## :fire: TableExtraction

TableExtraction

| Field Name | Type | Description |
|--|--|--|
| Column |  |  |

# Column

| Field Name | Type | Description |
|--|--|--|
| Key |  |  |
| Value |  |  |
