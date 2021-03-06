higml
=====

Overview
--------

Higml is a concise format for "cascading configuration" - it uses cascading rules to create an output hash from an input hash.

Example Higml and input:

    # Higml
    action:show
      :pageName Search Results
      :keywords= keywords

      filter
        :filter_terms= filter_terms
    
    # Input hash
    {
      :action => "show",
      :filter => "any value"
    }
    
This output is produced:

    {
      :pageName => "Search Results",
      :keywords => ... #depends on value of "keywords" method in given context
      :filter_terms => ... #depends on value of "filter_terms" method in given context
    }

To get a better understandung of _what_ Higml does, go through spec/higml_spec.rb and the corresponding .higml files in spec/fixtures.

Why would you want to use it? The original use case was generating a json object for analytics. The json object had different fields and values depending on what section of the site and individual page were being viewed. When viewing a profile, the json might be

    {
      pageName: "Profile: 4320",
      section: "Profiles"
    }
    
whereas search might look like

    {
      pageName: "Search: 'robert'",
      section:  "Search"
    }
    
The actual json was much more complicated, containing about 20 keys. Writing the mapping between input and output in separate higml files (one for each Rails controller) allowed my coworkers and I to quickly understand what the json would be for a given page, or why the json was the way it was. Example .higml files can be found in /examples.

Syntax
------

Example:

    @import application.higml
    action:show, action:new
      :pageName Search Results
      :keywords= keywords
      
      q:rutabagas
        :rutabagas true
      
      filter
        :filter_terms= filter_terms

<dl>
  <dt>@import appication.higml</dt>
  <dd>acts as if the contents of application.higml were located where @import is</dd>
  <dt>action:show, action:new</dt>
  <dd>If the input hash has a value of "show" _or_ "new" for the key :action, then use the mapping that follows</dd>
  <dt>:pageName Search Results</dt>
  <dd>Output hash has a value of "Search Results" for the :pageName key; constant string</dd>
  <dt>:keywords= keywords</dt>
  <dd>Value of :keywords in output hash is whatever "keywords" evaluates to in the Higml mapper_context; let's you have dynamic values</dd>
  <dt>q:rutabagas</dt>
  <dd>If input hash satisfies action:show _or_ action:new, _and_ it has a value of "rutabagas" for :q, then apply the mapping that follows</dd>
  <dt>filter</dt>
  <dd>If input hash has a key of :filter at all (regardless of :q but dependent on :action), then apply mapping that follows</dd>
</dl>

Config
------
Use Higml.config.x to set these

<dl>
  <dt>config_directory</dt>
  <dd>Where higml-config.yml is stored #this may be overkill :(</dd>
  <dt>higml_directory</dt>
  <dd>Where all the .higml files are stored</dd>
  <dt>global_pairs</dt>
  <dd>should be hash; used as base hash that result of mapping is merged on to</dd>
</dl>

Contributing to higml
---------------------
 
* Check out the latest master to make sure the feature hasn't been implemented or the bug hasn't been fixed yet
* Check out the issue tracker to make sure someone already hasn't requested it and/or contributed it
* Fork the project
* Start a feature/bugfix branch
* Commit and push until you are happy with your contribution
* Make sure to add tests for it. This is important so I don't break it in a future version unintentionally.
* Please try not to mess with the Rakefile, version, or history. If you want to have your own version, or is otherwise necessary, that is fine, but please isolate to its own commit so I can cherry-pick around it.

Copyright
---------

Copyright (c) 2010 Daniel Higginbotham. See LICENSE.txt for
further details.

