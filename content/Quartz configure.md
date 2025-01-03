
Okay, here's a breakdown of how to install Quartz and publish your Obsidian vault online for free, based on the video you provided. This is a more involved process than using Obsidian Publish directly, but it allows for more customization and control while being free.

**The Core Idea**

The video explains that the core idea is to use the following tools:

*   **Quartz:** A static site generator based on Hugo, that takes markdown files and creates a website.
*   **Obsidian Export:** A library that exports markdown files from an Obsidian vault, while filtering out specific files and folders.
*   **Hugo Obsidian:** a tool to parse Obsidian markdown files so Hugo can understand them.
*   **[[Git-GitHub]]** For version control and hosting (GitHub Pages).

**Here's a step-by-step guide, incorporating the key information from the video:**

**Prerequisites**

1.  **Basic Git and GitHub Knowledge:** You'll need to know how to clone repos, commit changes, and push code.
2.  **Basic Terminal/Command Line Skills:** You'll be using the terminal to run commands.
3. **Install Node.js** You will need node.js installed for some of the commands.
4.  **Obsidian Vault:** You should have your Obsidian vault with the notes you want to publish.

**Step 1: Set up your environment**

1.  **Clone the Repositories:** You will need to clone three repositories, the order does not matter.
    *   **Quartz:** Go to [https://github.com/jackyzha0/quartz](https://github.com/jackyzha0/quartz) and clone the repo locally to your computer using `git clone https://github.com/jackyzha0/quartz`.
    *   **Obsidian Export:** Go to [https://github.com/nickgroening/obsidian-export](https://github.com/nickgroening/obsidian-export) --> ( https://github.com/zoni/obsidian-export.git) and clone the repo locally to your computer using `git clone https://github.com/nickgroening/obsidian-export`.
    *   **Hugo Obsidian:** Go to [https://github.com/sissbruecker/hugo-obsidian](https://github.com/sissbruecker/hugo-obsidian) --> (https://github.com/jackyzha0/hugo-obsidian) and clone the repo locally to your computer using `git clone https://github.com/sissbruecker/hugo-obsidian`.
    * It is recommended to keep all three repositories in the same parent folder.

2. **Install Dependencies:**
    *   Navigate to the **Quartz** directory in your terminal and run `npm install` to install the required node modules.

**Step 2: Configure Obsidian Export**

1.  **Create `.export-ignore` File:** In the root directory of your **Obsidian vault**, create a file named `.export-ignore`.
2.  **Add Files/Folders to Ignore:** In the `.export-ignore` file, list the folders and files you DO NOT want to publish. Each item on a new line.

    ```
    templates
    private
    collections
    people
    ```

**Step 3:  Configure Quartz**
1.  Navigate to the **Quartz** directory.
2. Open the **quartz.config.ts** file.
3.  Edit the **configuration** section to change:
    *   **`siteTitle`** to your site name
    *   **`description`** to a description for your site
    *   **`logo`** to your site logo or remove this for no logo
    * **`analytics`** Remove to disable analytics

**Step 4: Create a Script**

1. **Create a Watch Script:** In the root folder where you have the **quartz**, **obsidian-export** and **hugo-obsidian** directories. create a file named `watch.sh`
2.  **Add the following code:**

    ```bash
    #!/bin/bash

    # Variables
    QUARTZ_DIR="./quartz"
    OBSIDIAN_VAULT_DIR="/path/to/your/obsidian/vault"  # Replace with the actual path
    HUGO_OBSIDIAN_DIR="./hugo-obsidian"
    
    # Start Nodemon - Watch for changes and rebuild
    echo "Starting nodemon..."
    nodemon --watch "$OBSIDIAN_VAULT_DIR" \
            --watch "$QUARTZ_DIR/javascript" \
            --watch "$QUARTZ_DIR/styles" \
            --watch "$QUARTZ_DIR/layouts" \
            --watch "$QUARTZ_DIR/config.toml" \
            --watch "$QUARTZ_DIR/config.yaml" \
            --ext "md,html,js,scss,xml" \
            --exec "./compile.sh"
    ```
    *   Replace `/path/to/your/obsidian/vault` with the actual path to your Obsidian vault.
    *   Ensure you have `nodemon` installed. If not install with `npm install -g nodemon`.

3. **Create a Compile Script:** In the root folder where you have the **quartz**, **obsidian-export** and **hugo-obsidian** directories. create a file named `compile.sh`
4. **Add the following code:**

    ```bash
    #!/bin/bash

    # Variables
    QUARTZ_DIR="./quartz"
    OBSIDIAN_VAULT_DIR="/path/to/your/obsidian/vault"  # Replace with the actual path
    HUGO_OBSIDIAN_DIR="./hugo-obsidian"

    # Change into Hugo Obsidian Directory
    cd "$HUGO_OBSIDIAN_DIR" || exit 1

    # Delete old content
    rm -rf "./output"
    
    # Change into Quartz Directory
    cd "$QUARTZ_DIR" || exit 1
    
    # Delete old public
    rm -rf "./public"


    # Change back into the root directory
    cd .. || exit 1
    
    # Run Obsidian Export
    echo "Running Obsidian Export..."
    "$HUGO_OBSIDIAN_DIR/obsidian-export" \
        --add-titles \
        "$OBSIDIAN_VAULT_DIR" \
        "$QUARTZ_DIR/content"

    # Run Hugo Obsidian
    echo "Running Hugo Obsidian..."
    "$HUGO_OBSIDIAN_DIR/hugo-obsidian" \
        "$QUARTZ_DIR/content" \
        --assets-indices \
        --output "$QUARTZ_DIR"

    # Minify the result
    echo "Minifying..."
    cd "$QUARTZ_DIR" || exit 1
    hugo -minify

    echo "Done!"
    ```
    * Replace `/path/to/your/obsidian/vault` with the actual path to your Obsidian vault.
5. Make the Scripts executable
    * `chmod +x watch.sh`
    * `chmod +x compile.sh`

**Step 5: Run and Preview Locally**

1.  **Start Watch Script:** In your terminal, in the root folder run the command `./watch.sh`
2.  **Start Hugo Server** In a new terminal window, navigate to your Quartz directory then run the command `npm run dev`
3. **Preview** Open your web browser and go to `http://localhost:1313/` to preview your site.
   *   As you make changes to your Obsidian vault, the site preview in your browser will update.

**Step 6:  Prepare for Deployment**

1.  **Create Two GitHub Repositories:**
    *   **Repo 1 (Quartz Layout):** This will store your Quartz layout configuration, custom CSS, etc.
    *   **Repo 2 (Your Garden):** This will store the content that will become your website and be deployed to Github pages.
2.  **Initialize Git in Quartz Directory:**
    *   Navigate to your **Quartz** directory in the terminal.
    *   Run `git init`, `git add .`, `git commit -m "Initial Quartz Layout"` to initialize git.
    * Add the remote with `git remote add origin <URL_OF_YOUR_REPO_1>`.
    *   Run `git push -u origin main` to push to the first github repository you created.

**Step 7: Deploy to GitHub Pages**

1.  **Configure GitHub Pages:**
    *   Go to your **second repo (Your Garden)** on GitHub.
    *   Navigate to "Settings" > "Pages".
    *   Under "Source," choose "Deploy from a branch".
    *   Select your main branch (or the branch you use for publishing) and root as the folder.
    *   (Optional) Set up a custom domain, if you have one (as mentioned in the video).
2. **Test the deployment:** In the **Quartz** directory, navigate to the public folder, initialize Git again, then add, commit and push the contents of the folder to your second repository.

    *   After that, it can take a minute or two for GitHub to update your site

**Important Notes**

*   **Private Content:** The video's approach using `obsidian-export` and the `.export-ignore` file is crucial for keeping private content out of your public-facing website.
*   **Two Repos:** It's crucial to use two repositories. One for Quartz, and another that the contents of the `public` directory generated by Hugo will be pushed to.
*   **GitHub Actions:** In the video, they use the GitHub Actions built into Quartz. This step was removed as we are using Obsidian Export.

**In Summary**

This process involves a bit of setup, but it gives you a free way to publish your Obsidian vault online. This is a good option if you want more control over your site than what you get from Obsidian Publish, and if you have private content you need to exclude. Always start with a small number of pages to test the workflow.

I hope this helps! Let me know if you have any more questions.
