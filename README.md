<p><a target="_blank" href="https://app.eraser.io/workspace/ksIpClMCjui2MBeBuuy5" id="edit-in-eraser-github-link"><img alt="Edit in Eraser" src="https://firebasestorage.googleapis.com/v0/b/second-petal-295822.appspot.com/o/images%2Fgithub%2FOpen%20in%20Eraser.svg?alt=media&amp;token=968381c8-a7e7-472a-8ed6-4a6626da5501"></a></p>

In this project, we're going to fine-tune two open source models for machine translation task. 



## Project stack
- **Postgres** as database
- **Google Colab** as computational resource
## Project structure 
the projects starts with it's dataset. we have two main dataset in Persian and Arabic. they are two tsx or csv files. 



at fist all the dataset will load from tsv or csv files into postgres tables. 







## database model
```
title Database diagram
typeface clean
styleMode watercolor

dataset_editions [icon: check, color: blue] {
  id number pk
  name string
  description string
  link string
}

dataset [icon: file-text, color: blue] {
  id number pk
  edition_id id not null
  source_text text
  source_paragraph number
  source_reference string
  official_translate string
  source_language string
  is_enable boolean
}
dataset.edition_id > dataset_editions.id

// training_prompt_template is a string and include $variable_name to show which dataset table data should inject into the prompt 
tuning_configs [icon: tool, color: yellow] {
  id number pk
  description string
  llm_model string not null
  training_prompt_template string not null
  llm_tuning_configs json not null
  tuning_methodology string
}

tuning_dataset_distribution [icon: package, color: yellow] {
  id number pk
  tuning_config_id number
  dataset_record_id number
}
tuning_dataset_distribution.tuning_config_id > tuning_configs.id
tuning_dataset_distribution.dataset_record_id > dataset.id

fine_tuned_models [icon: ai, color: red] {
  id number pk
  tuning_config_id number
  pretrained_model_dir_path string
}
fine_tuned_models.tuning_config_id > tuning_configs.id

translation_method [icon: function] {
  id number pk
  name string
  fine_tuned_models_id number
}
translation_method.fine_tuned_models_id  < fine_tuned_models.id

translation_execution [ icon: function, color: purple] {
  id number pk
  name string
  description string
}

translation_execution_models [icon: link] {
  id number pk
  translation_execution_id number
  translation_method_id number
}
translation_execution_models.translation_execution_id  > translation_execution.id
translation_execution_models.translation_method_id > translation_method.id

translation [icon: language, color: orange] {
  id number pk
  translation_method_id number
  tuning_dataset_distribution_id number
}
translation.translation_method_id  > translation_method.id
```
```
-- Create the 'llm' schema
CREATE SCHEMA IF NOT EXISTS llm;

-- Table: dataset_editions in llm schema
CREATE TABLE IF NOT EXISTS llm.dataset_editions (
    id SERIAL PRIMARY KEY,
    name VARCHAR NOT NULL,
    description TEXT,
    link VARCHAR
);

-- Table: dataset in llm schema
CREATE TABLE IF NOT EXISTS llm.dataset (
    id SERIAL PRIMARY KEY,
    edition_id INT NOT NULL,
    source_text TEXT,
    source_paragraph INT,
    source_reference VARCHAR,
    official_translate VARCHAR,
    source_language VARCHAR,
    is_enable BOOLEAN,
    FOREIGN KEY (edition_id) REFERENCES llm.dataset_editions(id)
);

-- Table: tuning_configs in llm schema
CREATE TABLE IF NOT EXISTS llm.tuning_configs (
    id SERIAL PRIMARY KEY,
    description TEXT,
    llm_model VARCHAR NOT NULL,
    training_prompt_template TEXT NOT NULL,
    llm_tuning_configs JSON NOT NULL,
    tuning_methodology VARCHAR
);

-- Table: tuning_dataset_distribution in llm schema
CREATE TABLE IF NOT EXISTS llm.tuning_dataset_distribution (
    id SERIAL PRIMARY KEY,
    tuning_config_id INT,
    dataset_record_id INT,
    FOREIGN KEY (tuning_config_id) REFERENCES llm.tuning_configs(id),
    FOREIGN KEY (dataset_record_id) REFERENCES llm.dataset(id)
);

-- Table: fine_tuned_models in llm schema
CREATE TABLE IF NOT EXISTS llm.fine_tuned_models (
    id SERIAL PRIMARY KEY,
    tuning_config_id INT,
    pretrained_model_dir_path VARCHAR,
    FOREIGN KEY (tuning_config_id) REFERENCES llm.tuning_configs(id)
);
```



<!-- eraser-additional-content -->
## Diagrams
<!-- eraser-additional-files -->
<a href="/README-Database diagram-1.eraserdiagram" data-element-id="VG6Hi6Krg3AcAKXZ-irRC"><img src="/.eraser/ksIpClMCjui2MBeBuuy5___IadufevN5oYO4XeTK6Z8OhMAcK83___---diagram----c5c117ac29b1faa85ff2eddf4783d5a1-Database-diagram.png" alt="" data-element-id="VG6Hi6Krg3AcAKXZ-irRC" /></a>
<!-- end-eraser-additional-files -->
<!-- end-eraser-additional-content -->
<!--- Eraser file: https://app.eraser.io/workspace/ksIpClMCjui2MBeBuuy5 --->