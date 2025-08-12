I would like to create a plan for this repository to create a devcontainer.

We should use the knowledge gained while building the `https://github.com/AlexChesser/xformers-devcontainer` repo to ensure we appropriately layer our builds.

Our plan should read @.devcontext/architecture/onboarding/01-developer-setup-and-workflow.md and design from the start to adopt parameters which will allow for the specification of GPU or CPU only.

Account for a structure and design that will allow us to eventually adopt all the existing types of architecture included in this research @.devcontext/issues/1/03-cpu-architecture.md 

Our initial plan is targetting an MVP as discussed in this @.devcontext/issues/1/01-issue.txt

We should plan from the start to build a modular design. Making use of the devcontainer.local.json override might be a great way for us to offer suers "preconfigured" options that support their architecture. For example an override file for `devcontainer.ACCEL_HPU.json` with instructiosn to the user to copy and paste the file overtop of `devcontainer.local.json` - we could further make the default a CPU_ONLY version that runs for everyone while expecting `GPU_CUDA` users to swap in their file. 

We should analyze the existing docker files within the `docker` folder.  Perhaps even re-using our config override files to pre-specify the file that theyshould build "FROM" if that is possible to specify in a devcontainer.json file and override, that might be a really big win.

Read the @CONTRIBUTING.md to determine additonal tools that should exist on the container, perhaps there are linters or pre-commit hooks that need to be installed.

Break the work into small, logical units
 (use trunk-based development).

Ensure the plan specifies a series of
 individual pull requests.
 
Each step in the plan should include a
 justification for why it is safe to 
 deploy to production.


Organize parallelizable work into tracks.

Optimize the plan for "the three reads of
design":

Start with a high-level executive summary.

Next, a bullet-point ToC of phases, tracks,
 and PRs (with descriptions, anchor links).
 At the highest level, this should be suitable
 for someone to be able to hold the whole
 project in their head. One or two sentences
 at most.

Finally, detailed instructions per PR: 
 highlight the specific files, objects,
 and paths to be touched (use code formatting).

Don't include large code samples, when the PRs
 come along the code will be there and we want
 to eliminate redundancy for the humans doing
 the planning right now and code reviewers
 later. 
 
DO specify method signatures or API contracts.
 Using "API first" development philosophy
 guidance as found within the "15-factor-app"
 paper on development best practices.

DO provide a concise, human readable description of each PR where you explain what is being done at a "summary level" listing filenames and model changes is not enough. We must explain to the human reading this plan what we're trying to do and the goals we're trying to achieve in the PR.

Write your output to [03-initial-plan.md] in
 the same folder.