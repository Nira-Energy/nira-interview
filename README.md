## Get setup to develop for the take home assignment.

First, fork the nira-interview repo into your own repo.

1. <b>Install Pyenv to switch between different Python versions.</b> <br>
   https://github.com/pyenv/pyenv, windows: https://github.com/pyenv-win/pyenv-win
2. <b>Install the python version specified in <code>/nira-interview/.python-version</code> using Pyenv.</b> <code> pyenv install 3.9.6 </code>
3. <b>Navigate into the /nira-interview directory and double check that you've set up pyenv correct. </b> <br>
   When you run <code>python --version</code>, the version should match the version specified at <code>/nira-interview/.python-version</code>, which is 3.9.6 <br>
   Pyenv works by reading the .python-version file and automaticaly switching to the right python version.
4. <b>Install poetry</b> https://python-poetry.org/docs/
5. <b>Configure Poetry to create .venv folders in the project</b> <code>poetry config virtualenvs.in-project true</code>
6. <b>Navigate into the pipeline folder</b> <code>cd /nira-interview/pipeline</code>
7. <b>Install dependencies</b> <code>poetry install</code>
8. <b>Activate the virtual environment.</b> <code> poetry shell </code>
9. <b>Double check that the right version of python is being used in the virtual environment.</b> <code>python --version</code>
10. <b>Make sure that the dependencies were installed.</b> <code>poetry show</code>
11. <b>Spin up dagit. </b> <code>poetry run dagit</code>
12. <b>Navigate to <code>localhost:3000</code>. You should see dagster running there </b>
13. <b>In the jobs pane on the left, click the "nira_smoke_test_job" job. </b> Click "Launchpad" and then "Launch run". You should see the job print "Successfully ran smoketest".
14. <b>Specify python interpreter in VSCode</b> You should open the setting in VScode to "Python: Select interpreter". Input your own path, which should be <code>./pipeline/.venv/bin/python</code>
15. <b>You should be ready if you get here</b>

---

## Introduction to Dagster

Dagster is an open source tool we use to orchestrate our pipelines. You can learn more about Dagster at dagster.io. They're an awesome company.

Dagster jobs are essentially a list of steps written in pipeline. Each step is called an op. If you open <code>smoke_test_job.py</code>, you'll see the <code>nira_smoke_test_job</code> python definition which is annotated with @job.

The job is made from a series of calls to ops. The two ops are also defined there. The output of <code>smoke_test_op1</code> is passed into <code>smoke_test_op2</code>.

Its that easy, Dagster jobs are constructed from ops.

---

## Your assignment

Your task is to edit the <code>interview_job</code> defined in <code>interview_job.py</code>. First, lets see whats going on inside of interview_job.

1. First, we read in a raw CSV of buses we need to run the pipeline on in <code>raw_buses_to_run</code>.
2. Then we calculate the MW available for each bus <code>get_mw_available_for_each_bus_very_slow</code>. You can see in the code that calculating this takes 5 minutes per bus! Super slow.
3. Then we convert MW to GW in <code>add_gw_available_column</code>.
4. Lastly, we write the final DF to disk in <code>output_interview_job</code>.

This pipeline has already been run and has results inside of <code>pipeline/interview_job/output</code>. This pipeline has been run the slow way with the initial set of buses.

### Modifications needed

Sometimes, we have a new bus we need to run as well. But we don't want to rerun all the buses because that's too slow.

Your task is to figure out how to construct this pipeline so that we don't have to rerun all the buses, only the new ones, while still outputting one single CSV to disk.

A few constraints:

- You can tweak <code>get_mw_available_for_each_bus_very_slow</code> for testing purposes, but you are not allowed to change the code inside this file in the final submission. Don't get clever and just decrease the sleep() call to one second.
- We are only ever adding new buses, you do not need to worry about buses being removed. 
- For any given bus, the values calculated in <code>get_mw_available_for_each_bus_very_slow</code> will always be exactly the same (you can see this in the code).

Final deliverable:

- Send over a link to the forked repo you made the modifications in.
- Inside <code>raw_buses_to_run.py</code>, comment out line 4, and uncomment line 5. This will switch the raw buses csv to a new csv. You can go look in the CSVs, the only difference is one additional bus in the new one. Remember, there will only ever be bus additions in the new csv.
- There should be only one new file inside the /output folder that contains all the buses results for the buses defined in <code>new_raw_buses.csv</code>. You should delete the original csv in the output folder that the repo started with. There should never be 2 csv's in the output folder.
- Any new ops you need should be added to <code>interview_job.py</code> and also be implemented in their own file in the /ops folder.
