## License Options Breakdown

### Open/Permissive

- **MIT**: Super permissive, basically "do whatever but keep my name on it"
    - [Full License Text](https://opensource.org/licenses/MIT)
    - Pros: Simple, widely used, minimal restrictions
    - Cons: No patent protection, weak attribution requirements
      
- **Apache 2.0**: Like MIT but with patent protection (good for startups!)
    - [Full License Text](https://www.apache.org/licenses/LICENSE-2.0)
    - Pros: Patent protection, defensive termination, modification documentation
    - Cons: More complex than MIT
      
- **BSD-3**: Similar to MIT but with extra "don't use my name to promote" clause
    - [Full License Text](https://opensource.org/licenses/BSD-3-Clause)
    - Pros: Prevents others from using your name to endorse products
    - Cons: No patent protection

### Copyleft

- **GPL-3.0**: Forces derivatives to also be open source (viral)
    - [Full License Text](https://www.gnu.org/licenses/gpl-3.0.en.html)
    - Pros: Ensures all derivative works stay open
    - Cons: Can reduce adoption for libraries, no SaaS protection
      
- **LGPL-3.0**: Like GPL but allows linking without "infecting" the linked code
    - [Full License Text](https://www.gnu.org/licenses/lgpl-3.0.en.html)
    - Pros: Good for libraries, allows proprietary programs to use your code
    - Cons: Weaker copyleft protection than GPL
      
- **AGPL-3.0**: Extra strong copyleft that covers network usage (closes the SaaS loophole)
    - [Full License Text](https://www.gnu.org/licenses/agpl-3.0.en.html)
    - Pros: Prevents SaaS exploitation, strongest open source protection
    - Cons: Some companies ban AGPL code due to compliance concerns

### Middle Ground

- **MPL-2.0**: File-level copyleft, good middle ground
    - [Full License Text](https://www.mozilla.org/en-US/MPL/2.0/)
    - Pros: Allows mixing with proprietary code at the file level
    - Cons: More complex compliance monitoring

### For Assets/Content

- **CC BY**: Attribution required, commercial use allowed
    - [Full License Text](https://creativecommons.org/licenses/by/4.0/)
    - Pros: Wide adoption, simple attribution requirements
    - Cons: Allows commercial use without restrictions
      
- **CC BY-SA**: Attribution required, derivatives must use same license
    - [Full License Text](https://creativecommons.org/licenses/by-sa/4.0/)
    - Pros: Ensures derivatives stay open, allows commercial use, requires "appropriate form" for modifications
    - Cons: Not compatible with some other licenses
      
- **CC BY-NC**: Attribution required, no commercial use
    - [Full License Text](https://creativecommons.org/licenses/by-nc/4.0/)
    - Pros: Prevents commercial exploitation
    - Cons: Limits legitimate small-scale monetization (streamers, etc.)

## Opinionated Ideal License Features

### For Games

- Mandatory open source for all code (including mods/plugins)
- Networked/SaaS loophole closed (server mods must be shared)
- Strong attribution requirements
- Patent protection
- Allow gameplay streaming/content creation

### For Libraries/Plugins

- Allow use in both open and closed projects
- Modifications to the library must remain open
- Patent protection
- Clear attribution requirements

### For Assets

- Attribution required
- Derivatives must remain open
- Source files for assets must be provided (prevents encrypted-only distribution)
- Allow use in content creation (streams, reviews, etc.)
- Prevent asset ripping/reselling

## Ideal License Stack

| Project Type                                | Recommended License | Key Feature                                                                                          |
| ------------------------------------------- | ------------------- | ---------------------------------------------------------------------------------------------------- |
| Video Game Source Code                      | **AGPL-3.0**        | Ensures all code stays open, including any expansions, mods and forks. Even for networked/SaaS uses. |
| Video Game Assets and Documentation         | **CC BY-SA 4.0**    | Requires attribution & sharing alike.                                                                |
| Libraries, Godot Addons, Bevy Plugins, etc. | **LGPL-3.0**        | Allows inclusion in all types of projects without "infecting" it.                                    |

For the ultimate protection, consider combining these with explicit patent provisions from **Apache 2.0** and endorsement protection from **BSD-3**.

## Common License Concerns Explained

### Attribution

Ensures you get credit for your work. MIT requires maintaining copyright notice, CC licenses have specific attribution requirements. [More on attribution requirements](https://opensource.guide/legal/#does-your-project-have-any-dependencies)

### Patent Trolls

Companies that buy patents to sue others. Apache 2.0 offers protection via explicit patent grants. [Patent protection in licenses](https://opensource.org/faq#patents)

### Copyleft

Requires that derivatives of your work maintain the same license, keeping the code open. [Understanding copyleft](https://www.gnu.org/licenses/copyleft.en.html)

### License "Infection"

How copyleft licenses can require other code to adopt the same license when combined. [More on license compatibility](https://www.gnu.org/licenses/license-compatibility.html)

### SaaS Loophole

Using modified GPL code on a server without distributing it can avoid sharing requirements. AGPL closes this. [The SaaS loophole explained](https://www.gnu.org/licenses/why-affero-gpl.html)

### Commercial Use

Whether others can make money from your work. All standard open source licenses permit commercial use. [More on commercial use restrictions](https://opensource.org/faq#commercial)

## Organizations That Can Help

- [Software Freedom Conservancy](https://sfconservancy.org/) - Helps FOSS projects with legal and licensing support
- [Software Freedom Law Center](https://www.softwarefreedom.org/) - Legal services for open source projects
- [Open Source Initiative](https://opensource.org/) - Stewards the Open Source Definition
- [Free Software Foundation](https://www.fsf.org/) - Maintains the GPL family of licenses
- [Creative Commons](https://creativecommons.org/) - Support for content licensing

## Useful Resources

- [Choose A License](https://choosealicense.com/) - Simple license selection guide
- [TLDRLegal](https://tldrlegal.com/) - Plain-language license summaries
- [Open Source Guide](https://opensource.guide/legal/) - GitHub's guide to open source legalities
- [License Compatibility Wiki](https://wiki.creativecommons.org/wiki/License_Compatibility) - Understanding which licenses work together
- [GNU License FAQ](https://www.gnu.org/licenses/gpl-faq.html) - Detailed explanations of GPL licenses
- [OSI License List](https://opensource.org/licenses) - Comprehensive list of approved open source licenses
- [Public Domain Alternatives](https://creativecommons.org/share-your-work/public-domain/) - Options for dedicating work to the public domain
