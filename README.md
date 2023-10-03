# Sidebar Menu Component

This package helps you create a sidebar menu while keeping file-system auto-nav routes.

## Installation

Install the package using npm:

```
npm i astro-sidebar-menu
```

## Usage

1. Import the component in your Astro site layout file.

   ```
   import SidebarComponent from "astro-sidebar-menu/astro";
   import generateMenuData from '../utility';

   // Generate the menu data based on your project's file structure
   const menuData = generateMenuData('<directory name>');
   ```

Create a file named utility.js in the root directory of your project and add the following code to generate the menu data:


```
import fs from "fs";
import path from "path";

function generateMenuData(basePath, directory = "") {
  const menuData = [];

  // Read the contents of the base directory
  const files = fs.readdirSync(basePath);

  // Filter out directories
  const directories = files.filter((file) =>
    fs.statSync(path.join(basePath, file)).isDirectory()
  );

  // Filter out Markdown files (add more extensions as needed)
  const markdownFiles = files.filter(
    (file) => file.endsWith(".md") || file.endsWith(".mdx")
  );

  // Create menu items for each directory
  directories.forEach((dir) => {
    const subPath = path.join(basePath, dir);
    const submenus = generateMenuData(subPath, path.join(directory, dir)); // Pass the updated directory

    menuData.push({
      label: dir,
      submenus: submenus.length > 0 ? submenus : undefined,
    });
  });

  // Include Markdown files as menu items
  markdownFiles.forEach((file) => {
    const label = file.replace(/\.(md|mdx)$/, ""); // Use the file name without the extension as the label
    menuData.push({
      label,
      url: path.join("/", directory.replace(basePath, ""), label),
    });
  });

  return menuData;
}

export default generateMenuData;
```

Finally, you can call the component in your layout file and customize the styles if needed:


```
<SidebarComponent customLiClass="sidebar-menu-li" customUlClass="sidebar-menu-ul" menuData={menuData} />
```

You can add custom style adjustments by passing custom `<ul>` and `<li>` classes. If not passed, default styles will be used. Additionally, you can adjust the redirection links of each menu click in the utility.js file if the base path needs to be changed.

Feel free to customize and style your sidebar menu according to your project's requirements.