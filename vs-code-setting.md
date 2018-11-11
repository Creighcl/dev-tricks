# VS Code Extensions
- Cobalt2 Theme Official
- ESLint
- indent-rainbow
- Switch to test

# VS Code Settings

```

{
    "terminal.integrated.shell.windows": "C:\\Program Files\\Git\\bin\\bash.exe",
    "git.autofetch": true,
    "files.eol": "\n",
    "editor.tabSize": 2,
    "workbench.colorTheme": "Cobalt2",
    "createTest.testFolder": "test",
    "createTest.srcFolder": "src",
    "createTest.testFileExtension": "Spec.js",
    "createTest.testFileTemplate": [
        "import React from 'react';",
        "import { mount, configure } from 'enzyme';",
        "import Adapter from 'enzyme-adapter-react-16';",
        "import ${moduleName} from '${modulePath}'",
        "",
        "configure({ adapter: new Adapter() });",
        "",
        "describe('', () => {",
        "  describe('', () => {",
        "    it('', () => {",
        "",
        "    });",
        "  });",
        "});",
        ""
    ],
    "javascript.updateImportsOnFileMove.enabled": "always"
}

```