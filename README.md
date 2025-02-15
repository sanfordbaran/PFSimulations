# PFSimulations
#### <u>Description</u>

Given a distinct number of defined 'Group Types',  create simulations of how members of a particular 'Group Type' would rate each of  the 'Statements' in the survey.

#### <u>Definitions:</u>

`num_gts` => the number of group types

`num_ss` => the number of statements in the Survey

####  <u>Context</u>

A user is given a survey comprised of `num_ss` 'Statements'.  The user rates their level of agreement for each 'Statement' on a scale of -100 to +100 where -100 indicates complete disagreement and +100 indicates total alignment.  Heuristics have been developed to take an individual's rating of these `num_ss` statements and then classify them into one of the defined group types.

To test to see how well the heuristic works, we create simulations (using the OpenAI Completion API) of how a potential user from a given group would probably rate each of these 'Statements'.  We then take the simulated survey responses, run the heuristic on these simulated ratings and check to see if the heuristic actually identifies the correct group from which we used to produce the simulated ratings.

NOTE:  Since this is work that I am developing as a consultant for a private company, and this repo is a public repository I purposely omit in the README and python code,  details as to the particular 'group' types as well as the specifics of the survey questions.



#### <u>Inputs</u>

- 'Experiment Number' label
- 'Number of Simulations' per Group Type  (number of generated .xlsx files per Group Type) 



#### <u>Outputs</u>

- Folder 'data/E\<Experiment Number\>_PF_Simulated_Responses/' ... containing `num_gts` x \<Number of Simulations\>   .xlsx files.
  - Each file contains `num_ss + 1` rows (one row for the column headers ) and `num_ss` rows   ... one for each one of the `num_ss` Survey Statements
  - Each file has 3 columns
    - Statement
    - Rating
    - Rationale
- Folder 'txt_files/E\<Experiment Number\>/'  containing  `num_gts` number of group types x \<Number of Simulations\>  .txt files.
  - Each .txt file contains `num_ss` lines, where each line is a '|' delimited string composed using the values of 'rating' and 'explanation' coming from the OpenAI Completion API
- Log file 'E\<Experiment Number\>_pf_survey_simulated_responses.log'



#### <u>Usage</u>

`python pf_survey_simulated_responses.py --ex_num <Experiment Number> --sims <Number of Simulations per Group Type>`



#### <u>OpenAI Model Details</u>

- Model:  gpt-4o-mini
- Completion API Temperature:  1.4
- Approximate Time per Simulation: 2 minutes
- Usage Tier: 2   (which allows 5000 Completion calls per minute)



#### <u>Completion Prompt</u>

	Here is the definition of a <group_type>: <group_type_description>
	
	You will be provided with a statement.
	The statement will be delimited with '####' characters.
	
	On a scale of -100 to +100 how would such a member of type {group_type} rate this statement? 
	Where -100 indicates the least amount of agreement and +100 indicates the most amount of agreement.
	
	I need you to provide two pieces of output:
	1. The rating (a numerical score between -100 and +100 inclusive).
	2. An explanation of why you gave that score in 40 words or less.
	
	Please provide two outputs in the form of a valid JSON object
	{{
	    "rating": <numerical_score>,
	    "explanation": <reason_for_score>
	}}
	
	- The "rating" must be a number between -100 and +100 inclusive.
	- The "explanation" should be a concise justification for the given rating in 40 words or less.
	
	Make sure the output is a properly formatted JSON object.

