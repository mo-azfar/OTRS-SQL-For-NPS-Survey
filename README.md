# OTRS-SQL-For-NPS-Survey-
- OTRS SQL for getting NPS types survey data
- For OTRS 6 CE


1. Setup the OTRS Survey with NPS type question
	
		Example of NPS Value:
		
		Good 
		Average
		Poor


2. 	[LIST]

		SELECT CONCAT_WS(' ', u.first_name, u.last_name) AS TICKET_OWNER,
		sr.ticket_id AS TID, 
		t.tn AS TICKET_NUMBER, 
		sq.question AS QUESTION,
		sv.vote_value AS VOTED, 
		sa.answer AS ANSWER,
		sr.vote_time AS TIME
		FROM survey_request sr
		LEFT JOIN survey_vote sv ON (sr.id=sv.request_id)
		LEFT JOIN survey_answer sa ON (sv.vote_value=sa.id AND sv.question_id=sa.question_id)
		JOIN ticket t ON (sr.ticket_id=t.id)
		JOIN users u ON (t.user_id=u.id)
		JOIN survey_question sq ON (sr.survey_id=sq.id AND sq.question_type IN ('NPS'))	


3. [COUNT BY AGENT]

		SELECT TICKET_OWNER,
		COUNT(DISTINCT(TID)) AS SENT_SURVEY_BY_TICKET,
		COUNT(DISTINCT(CASE WHEN TIME IS NOT NULL THEN TID ELSE NULL END)) AS ANSWERED_SURVEY,
		COUNT(CASE WHEN ANSWER IN ('Good') THEN VOTED ELSE NULL END) AS GOOD,
		COUNT(CASE WHEN ANSWER IN ('Average') THEN VOTED ELSE NULL END) AS AVERAGE,
		COUNT(CASE WHEN ANSWER IN ('Poor') THEN VOTED ELSE NULL END) AS POOR
		FROM (
		SELECT CONCAT_WS(' ', u.first_name, u.last_name) AS TICKET_OWNER,
		sr.ticket_id AS TID, 
		t.tn AS TICKET_NUMBER, 
		sq.question AS QUESTION,
		sv.vote_value AS VOTED, 
		sa.answer AS ANSWER,
		sr.vote_time AS TIME
		FROM survey_request sr
		LEFT JOIN survey_vote sv ON (sr.id=sv.request_id)
		LEFT JOIN survey_answer sa ON (sv.vote_value=sa.id AND sv.question_id=sa.question_id)
		JOIN ticket t ON (sr.ticket_id=t.id)
		JOIN users u ON (t.user_id=u.id)
		JOIN survey_question sq ON (sr.survey_id=sq.id AND sq.question_type IN ('NPS'))
		)
		AS SURVEY_RESULT
		GROUP BY TICKET_OWNER
		
		
[![sql1.png](https://i.postimg.cc/d15F4v6W/sql1.png)](https://postimg.cc/bDS4w7Kb)

