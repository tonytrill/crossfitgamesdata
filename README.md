# CrossFit Games API Data Project

### Data Collection

I built a Python Script to ping CrossFit's public API to collect leaderboard data from the 2018 Open. The API allows you to collect not only athlete specific data but their scores from the Open. 
I made additional fields to make the data exploration process a little easier. For example, I changed all weights of athletes to pounds, heights to inches, and I made sure to use the seconds elapsed for a workout. I also have a workout type, so the goal, i.e. For Time, AMRAP. I then created a score type. So if someone had a score type of 'time' and a workout type of time then they completed the workout in under the time cap. If the workout type is 'time' but the score type is rounds then the athlete did not complete the entire work in under the time cap.

### Data Exploration

TO DO
