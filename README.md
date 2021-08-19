# Broken installation demo

Here we see a demonstration of installing my for of [@windmill/react-ui](https://github.com/estevanmaito/windmill-react-ui).

In `./from_fork` the `package.json` looks like this:

```json
{
  "dependencies": {
    "@windmill/react-ui": "github:danhab99/windmill-react-ui"
  }
}
```

This will cause npm to install the package from my fork on github. But you will notice that inside it's `node_modules` the package is empty:

```
ls from_fork/node_modules/@windmill/react-ui
config.js  LICENSE  node_modules  package.json  README.md
```

Comparing the fork node modules to the source node modules we can see that there are alot of files that the fork didn't download.

```
ls from_source/node_modules/@windmill/react-ui/
CHANGELOG.md  config.js  dist  lib  LICENSE  node_modules  package.json  README.md
```

This is what I can't explain. I noticed this problem in my private project when nodejs could not resolve my fork of the package. It took me hours to track down the problem until I gave up late last night. There seems to be no explaination for this behavior. The best solution I can find is to install my fork from the github tarball. As we can see in `./from_tarball_fork` the `package.json` looks like this:

```json
{
  "dependencies": {
    "@windmill/react-ui": "https://github.com/danhab99/windmill-react-ui/tarball/master"
  }
}
```

And the `node_modules` look alot more promising:

```
ls from_tarball_fork/node_modules/@windmill/react-ui
CHANGELOG.md        LICENSE       package-lock.json  src            @types
CODE_OF_CONDUCT.md  node_modules  README.md          style          webpack.config.js
config.js           package.json  setupTests.ts      tsconfig.json
```

But the package doesn't build. When running the `npm run build` process manually we see that the tarball didn't install all of its dependencies:

```
 npm run build

> @windmill/react-ui@0.6.0 prebuild
> rimraf dist lib && npm run build:lib && npm run build:ts


> @windmill/react-ui@0.6.0 build:lib
> babel src/themes/default.ts --out-file lib/defaultTheme.js

Error: Couldn't find preset "@babel/preset-env" relative to directory "/home/dan/Documents/node/broken_install_demo/from_tarball_fork/node_modules/@windmill/react-ui"
    at /usr/lib/node_modules/babel-cli/node_modules/babel-core/lib/transformation/file/options/option-manager.js:293:19
    at Array.map (<anonymous>)
    at OptionManager.resolvePresets (/usr/lib/node_modules/babel-cli/node_modules/babel-core/lib/transformation/file/options/option-manager.js:275:20)
    at OptionManager.mergePresets (/usr/lib/node_modules/babel-cli/node_modules/babel-core/lib/transformation/file/options/option-manager.js:264:10)
    at OptionManager.mergeOptions (/usr/lib/node_modules/babel-cli/node_modules/babel-core/lib/transformation/file/options/option-manager.js:249:14)
    at OptionManager.init (/usr/lib/node_modules/babel-cli/node_modules/babel-core/lib/transformation/file/options/option-manager.js:368:12)
    at File.initOptions (/usr/lib/node_modules/babel-cli/node_modules/babel-core/lib/transformation/file/index.js:212:65)
    at new File (/usr/lib/node_modules/babel-cli/node_modules/babel-core/lib/transformation/file/index.js:135:24)
    at Pipeline.transform (/usr/lib/node_modules/babel-cli/node_modules/babel-core/lib/transformation/pipeline.js:46:16)
    at transform (/usr/lib/node_modules/babel-cli/lib/babel/util.js:50:22)
```

I can manually install `"@babel/preset-env"`, but then we get another problem. Webpack misscompiles the typescript:

```
npm run build

> @windmill/react-ui@0.6.0 prebuild
> rimraf dist lib && npm run build:lib && npm run build:ts


> @windmill/react-ui@0.6.0 build:lib
> babel src/themes/default.ts --out-file lib/defaultTheme.js


> @windmill/react-ui@0.6.0 build:ts
> tsc --declaration --declarationMap --emitDeclarationOnly


> @windmill/react-ui@0.6.0 build
> webpack

[webpack-cli] Compilation finished
assets by status 14.6 KiB [cached] 1 asset
runtime modules 931 bytes 4 modules
modules by path ./src/*.tsx 41.4 KiB 28 modules
./src/index.ts 1.42 KiB [built] [code generated]
./src/context/ThemeContext.tsx 497 bytes [built] [code generated] [1 error]

ERROR in ./src/Alert.tsx 5:0
Module parse failed: The keyword 'enum' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> enum AlertEnum {
|   success,
|   danger,
 @ ./src/index.ts 3:0-42 3:0-42

ERROR in ./src/Avatar.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface AvatarProps extends React.HTMLAttributes<HTMLDivElement> {
|   /**
|    * The size of the avatar
 @ ./src/index.ts 4:0-44 4:0-44

ERROR in ./src/Backdrop.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface BackdropProps extends React.HTMLAttributes<HTMLDivElement> {}
|
| const Backdrop = React.forwardRef<HTMLDivElement, BackdropProps>(function Backdrop(props, ref) {
 @ ./src/index.ts 5:0-48 5:0-48

ERROR in ./src/Badge.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface BadgeProps extends React.HTMLAttributes<HTMLSpanElement> {
|   /**
|    * The type of the badge
 @ ./src/index.ts 6:0-42 6:0-42

ERROR in ./src/Button.tsx 6:5
Module parse failed: Unexpected token (6:5)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import warn from './utils/warning'
|
> type IconType =
|   | string
|   | React.FunctionComponent<{ className: string; 'aria-hidden': boolean }>
 @ ./src/index.ts 7:0-44 7:0-44

ERROR in ./src/Card.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface CardProps extends React.HTMLAttributes<HTMLDivElement> {
|   /**
|    * Removes default styles (if true) so you can override with your own background styles
 @ ./src/index.ts 8:0-40 8:0-40

ERROR in ./src/CardBody.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLDivElement> {}
|
| const CardBody = React.forwardRef<HTMLDivElement, Props>(function CardBody(props, ref) {
 @ ./src/index.ts 9:0-48 9:0-48

ERROR in ./src/Dropdown.tsx 7:7
Module parse failed: Unexpected token (7:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import FocusLock from 'react-focus-lock'
|
> export interface DropdownProps extends React.HTMLAttributes<HTMLUListElement> {
|   /**
|    * Function executed when the dropdown is closed
 @ ./src/index.ts 10:0-48 10:0-48

ERROR in ./src/DropdownItem.tsx 6:5
Module parse failed: Unexpected token (6:5)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> type Ref = typeof Button
| const DropdownItem = React.forwardRef<Ref, ButtonProps>(function DropdownItem(props, ref) {
|   // Note: className is passed to the inner Button
 @ ./src/index.ts 11:0-56 11:0-56

ERROR in ./src/HelperText.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface HelperTextProps extends React.HTMLAttributes<HTMLSpanElement> {
|   /**
|    * Defines the color of the helper text (the same as with Input, Select, etc.)
 @ ./src/index.ts 12:0-52 12:0-52

ERROR in ./src/Input.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface InputProps extends React.ComponentPropsWithRef<'input'> {
|   /**
|    * Defines the color of the input
 @ ./src/index.ts 13:0-42 13:0-42

ERROR in ./src/Label.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface LabelProps extends React.HTMLAttributes<HTMLLabelElement> {
|   /**
|    * Applies specific styles for checkboxes
 @ ./src/index.ts 14:0-42 14:0-42

ERROR in ./src/Modal.tsx 9:7
Module parse failed: Unexpected token (9:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface ModalProps extends React.HTMLAttributes<HTMLDivElement> {
|   /**
|    * Function executed when the dropdown is closed
 @ ./src/index.ts 15:0-42 15:0-42

ERROR in ./src/ModalBody.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLDivElement> {}
|
| const ModalBody = React.forwardRef<HTMLDivElement, Props>(function ModalBody(props, ref) {
 @ ./src/index.ts 16:0-50 16:0-50

ERROR in ./src/ModalFooter.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLDivElement> {}
|
| const ModalFooter = React.forwardRef<HTMLElement, Props>(function ModalFooter(props, ref) {
 @ ./src/index.ts 17:0-54 17:0-54

ERROR in ./src/ModalHeader.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLDivElement> {}
|
| const ModalHeader = React.forwardRef<HTMLParagraphElement, Props>(function ModalHeader(props, ref) {
 @ ./src/index.ts 18:0-54 18:0-54

ERROR in ./src/Pagination.tsx 5:14
Module parse failed: Unexpected token (5:14)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> const PrevIcon: React.FC = function PrevIcon(props) {
|   return (
|     <svg {...props} aria-hidden="true" fill="currentColor" viewBox="0 0 20 20">
 @ ./src/index.ts 19:0-52 19:0-52

ERROR in ./src/Select.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface SelectProps extends React.ComponentPropsWithRef<'select'> {
|   /**
|    * Defines the color of the select
 @ ./src/index.ts 20:0-44 20:0-44

ERROR in ./src/Table.tsx 3:7
Module parse failed: Unexpected token (3:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import React from 'react'
|
> export interface TableProps extends React.TableHTMLAttributes<HTMLTableElement> {}
|
| const Table = React.forwardRef<HTMLTableElement, TableProps>(function Table(props, ref) {
 @ ./src/index.ts 21:0-42 21:0-42

ERROR in ./src/TableBody.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLTableSectionElement> {}
|
| const TableBody = React.forwardRef<HTMLTableSectionElement, Props>(function TableBody(props, ref) {
 @ ./src/index.ts 22:0-50 22:0-50

ERROR in ./src/TableCell.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.TdHTMLAttributes<HTMLTableCellElement> {}
|
| const TableCell = React.forwardRef<HTMLTableCellElement, Props>(function TableCell(props, ref) {
 @ ./src/index.ts 23:0-50 23:0-50

ERROR in ./src/TableContainer.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLDivElement> {}
|
| const TableContainer = React.forwardRef<HTMLDivElement, Props>(function TableContainer(props, ref) {
 @ ./src/index.ts 24:0-60 24:0-60

ERROR in ./src/TableFooter.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLDivElement> {}
|
| const TableFooter = React.forwardRef<HTMLDivElement, Props>(function TableFooter(props, ref) {
 @ ./src/index.ts 25:0-54 25:0-54

ERROR in ./src/TableHeader.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLTableSectionElement> {}
|
| const TableHeader = React.forwardRef<HTMLTableSectionElement, Props>(function TableHeader(
 @ ./src/index.ts 26:0-54 26:0-54

ERROR in ./src/TableRow.tsx 5:0
Module parse failed: The keyword 'interface' is reserved (5:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> interface Props extends React.HTMLAttributes<HTMLTableRowElement> {}
|
| const TableRow = React.forwardRef<HTMLTableRowElement, Props>(function TableRow(props, ref) {
 @ ./src/index.ts 27:0-48 27:0-48

ERROR in ./src/Textarea.tsx 5:7
Module parse failed: Unexpected token (5:7)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { ThemeContext } from './context/ThemeContext'
|
> export interface TextareaProps extends React.ComponentPropsWithRef<'textarea'> {
|   /**
|    * Defines the color of the textarea
 @ ./src/index.ts 28:0-48 28:0-48

ERROR in ./src/Transition.tsx 7:0
Module parse failed: The keyword 'interface' is reserved (7:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import { CSSTransition as ReactCSSTransition } from 'react-transition-group'
|
> interface TransitionContext {
|   parent: {
|     appear?: string
 @ ./src/index.ts 29:0-52 29:0-52

ERROR in ./src/Windmill.tsx 7:0
Module parse failed: The keyword 'interface' is reserved (7:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import useDarkMode from './utils/useDarkMode'
|
> interface Props extends React.HTMLAttributes<HTMLDivElement> {
|   children: React.ReactNode
|   /**
 @ ./src/index.ts 30:0-48 30:0-48

ERROR in ./src/context/ThemeContext.tsx 4:0
Module parse failed: The keyword 'interface' is reserved (4:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
| import defaultTheme from '../themes/default'
|
> interface ThemeContextInterface {
|   theme: any
|   mode?: Mode
 @ ./src/index.ts 1:0-72 1:0-72

webpack 5.1.3 compiled with 29 errors in 232 ms
```

This is about the point where I gave up. I did everything that I could think of and there's nothing more.
