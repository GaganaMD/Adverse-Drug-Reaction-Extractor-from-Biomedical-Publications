# Adverse-Drug-Reaction-Extractor-from-Biomedical-Publications


## About the Project

- Biomedical publications often contain valuable information about adverse drug reactions (ADRs).
- Extracting and analyzing these reactions is crucial for ensuring public drug safety, but manual coding of such reactions can be time-consuming and error-prone.
- The project aims to automate the extraction and encoding of adverse drug reactions using the DSPy framework. By leveraging in-context learning and retrieval techniques, we will develop a pipeline capable of:
    - Reading the biomedical papers
    - Predicting adverse reactions
    - Encoding them into a standardized set of reaction terms.

## Setting Up the Data

Dataset used: BioDEX

- The BioDEX dataset comprises biomedical publications along with expert-created drug safety reports.
- It provides us with a rich source of information regarding adverse reactions associated with various drugs and treatments.
    - [BioDEX GitHub](https://github.com/KarelDO/BioDEX)
    - [BioDEX HuggingFace](https://huggingface.co/BioDEX)
- We use helper functions to parse and normalize biomedical reactions.
- Each data point in the BioDEX dataset consists of:
    - Title
    - Abstract
    - Context or body of the paper
    - List of associated reactions.

## Setting Up Evaluation Code

Our primary evaluation metric will be Recall@10, which measures the proportion of true positive predictions found within the top 10 predictions made by our pipeline.

## Setting Up Neural Grounding

- **Challenge**: BioDEX dataset has 26,000+ reaction classes which makes it complex to handle predictions.
- **Solution**: We'll use a retrieval-based method called neural grounding.
- **How It Works**:
    - We embed reaction names using the BioLORD model.
    - For each ungrounded reaction prediction, we find its K nearest neighbors in the embedding space.
    - We can also use prior statistics from the BioDEX trainset to improve accuracy.
- **Benefits**:
    - Scalable method to handle the vast reaction space.
    - Enhances prediction accuracy with optional prior statistics.
- **Implementation Note**:
    - Utilizing GPU runtime can significantly speed up the embedding process, especially with new encoders like BioLORD.

## DSPy Program

DSPy program will follow a specific input-output behavior:

- **Input**: Title, abstract, and body of a biomedical paper
- **Output**: A list of predicted reactions, sorted by confidence level

We will design our pipeline as follows:

1. Chunk the paper into different windows.
2. Predict ungrounded reaction terms per chunk using in-context learning.
3. Find the top K nearest grounded neighbors per ungrounded reaction using the grounder defined above.
4. Resolve all grounded reactions into a final prediction using the similarity scores from the grounding step.

The abstractions provided by DSPy enable us to iterate quickly on the pipeline design and experiment with different approaches for resolving predictions.

## Implementation

- A signature for the `graphOfThought` in-context module is created, which encapsulates all the necessary components for predicting reactions without the need for complex prompt engineering.
- We next build the pipeline, `PredictThenGround`, which combines in-context learning, chunking, and optional grounding functions to predict and resolve adverse reactions efficiently.

## Evaluation

We will evaluate the performance of our pipeline using the BioDEX dataset, assessing its ability to predict adverse reactions accurately across multiple chunks of biomedical papers.
