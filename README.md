# Welcome to My Project 

\documentclass{article}
\usepackage{geometry}
\usepackage{listings}
\usepackage{hyperref}
\geometry{a4paper, margin=1in}

\title{ETL Pipeline Documentation: FRED Economic Data}

\begin{document}
\maketitle

\section*{Overview}
This documentation outlines the ETL (Extract, Transform, Load) pipeline designed to handle data sourced from the Federal Reserve Economic Data (FRED). The dataset focuses on outstanding commercial real estate loans held by all commercial banks, measured in billions of dollars across various dates. The pipeline is implemented in Python using a Google Colab notebook. Afterwards, the transformed data will be loaded into Google BigQuery for further analysis. Given that both are Google products, the entire pipeline creation process is more efficient.

\section*{Prerequisites}\label{sec:perrequisites}
Before running the ETL pipeline on Google Colab, please do the following:
\begin{itemize}
    \item Google Account: You need a Google account to access Google Colab and BigQuery.
    \item FRED API Key: Sign up for a FRED API account to obtain the API key. After signing up, obtain your API key from your FRED account.
    \item Google Cloud: Create a Google Cloud account and project. Then, enable the BigQuery API and obtain your project id from your Google Cloud account.
\end{itemize}

Note: Please make sure your Google Colab and Google Cloud are associated with the same email address.

\section*{ETL Process}
\subsection*{Data Ingestion}
\subsubsection*{Customizable Parameters}
When interacting with the FRED API, users have the flexibility to tailor the extraction process by customizing various parameters. The following parameters are available for customization:
\begin{itemize}
    \item \texttt{realtime\_start}: Specifies the start date for real-time data retrieval.
    \item \texttt{realtime\_end}: Specifies the end date for real-time data retrieval.
    \item \texttt{limit}: Sets the maximum number of data entries to get.
    \item \texttt{sort\_order}: Determines the sorting order of the data ('asc' for ascending, 'desc' for descending).
\end{itemize}
If users opt not to customize these parameters, default values are automatically applied from the parameter dictionary during the data extraction process. After customization, data is extracted from the FRED API in JSON format.

\subsection*{Data Transformation}
The transformation process is applied into two distinct data frames. The first data frame contains raw data such as date and loan amount. In the second data frame, the loan amounts are aggregated by year, and it consists of some fundamental insights such as the total, average, highest, and lowest values.

\subsubsection*{First DataFrame:}
\begin{itemize}
    \item Remove Redundant Columns: Eliminate redundant ‘observation\_start’ and ‘observation\_end’ columns, as these parameters were specified before data extraction.
    \item Column Renaming: Rename the 'value' column to 'loan\_amount' for clarity and alignment with the data.
    \item Data Type Adjustment: Change the data type of the 'loan\_amount' column to \texttt{float64}.
\end{itemize}

\subsubsection*{Second DataFrame:}
\begin{itemize}
    \item Date Formatting: Convert the 'date' column to a datetime format.
    \item Adding Year Column: Introduce a new column, 'Year,' extracting the year information from the 'date' column.
    \item Aggregation by Year: Aggregate the data by year, calculating various metrics such as sum, mean, max, min, and standard deviation for the 'Loan\_Amount\_in\_billion' column.
\end{itemize}

With both data frames now prepared, the next step is to load them into Google BigQuery for further analysis.

\subsection*{Data Loading}
The two data frames are loaded to the target database, Google Big Query. To maintain consistency, the schema design for the BigQuery tables align with the data types of the existing data frames. The for loop will automatically determine the column types of the existing data frames and apply them to the new tables. To ensure the data loaded successfully, a query is then executed to print a few lines from the loaded data. The loading process is facilitated by the \texttt{to\_gbq} function, specifying the project ID, dataset ID, table name, and an optional 'if\_exists' parameter to handle cases where the table already exists. For more information about \texttt{to\_gbq}, please see section \ref{reference}.

\section*{Running the ETL Pipeline on Google Colab}
To execute the ETL pipeline successfully, download the provided Python script from GitHub and run each cell in sequence. This step-by-step process will make the pipeline run smoothly. After completing the pipeline, you can proceed to analyze the transformed data in Google BigQuery. Additionally, you have the option to export the data to platforms such as Looker Studio for advanced visualization and analysis.

Note: Prior to running the ETL pipeline, please have all the necessary prerequisites (Section label{sec:perrequisites}).

\section*{Troubleshooting}
Please follow these steps:
\begin{itemize}
    \item Ensure that you have installed the required libraries by executing the following commands:
    \begin{lstlisting}[language=Python]
    !pip install pandas
    !pip install google-cloud-bigquery
    \end{lstlisting}
    \item Make sure to enter your Google Cloud project ID where indicated in the code.
    \item Confirm that your Google Colab email address matches the one you used to sign up for Google Cloud.
\end{itemize}

\section*{References}\label{reference}
\begin{itemize}
    \item \href{https://fred.stlouisfed.org/docs/api/fred/series_observations.html#frequency}{FRED API Series Observations Documentation}
    \item \href{https://pypi.org/project/google-cloud-bigquery/}{Google Cloud BigQuery Python Client Library}
    \item \href{https://pandas-gbq.readthedocs.io/en/latest/api.html#pandas_gbq.to_gbq}{pandas-gbq Documentation for to\_gbq Function}
\end{itemize}

Hope this helps!

\end{document}
