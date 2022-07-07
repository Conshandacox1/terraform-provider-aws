# Custom terraform aws provider.

This repo uses a forked terraform provider.
1. Clone https://github.com/paxosglobal/terraform-provider-aws
2. Run `make` (function: `go install`)
3. Create the local override: `mkdir -p ~/.terraform.d/plugins/registry.terraform.io/hashicorp/aws/4.9.111/darwin_amd64`
4. Copy the new binary: 
```
# if using asdf-installed golang
cp ~/.asdf/installs/golang/1.16.2/packages/bin/terraform-provider-aws ~/.terraform.d/plugins/registry.terraform.io/hashicorp/aws/4.9.111/darwin_amd64/
# -OR-
# if you have GOPATH set [to something special]
cp $GOPATH/bin/terraform-provider-aws ~/.terraform.d/plugins/registry.terraform.io/hashicorp/aws/4.9.111/darwin_amd64/
```


## NOTES:

#### Compile Terraform provider AWS
+ Ensure you know and understand any golang ENV variables you may have set, especially:
  * `$GOPATH`
  * `$GOBIN`

#### TF_CLI_CONFIG_FILE (`.terraformrc`)
+ Ensure you have correct configuration in your `~/.terraformrc` file, including the following:
  + [^Terraform Docs: CLI Configuration File]
    ```sh
    # @File: ~/.terraformrc
    plugin_cache_dir = "$HOME/.terraform.d/plugin-cache"
    disable_checkpoint = true
    ```

  + The following is optional. However, (as-is) simply defines default configuration

  + [^How to override a provider with a local version]
    ```sh
    # @File: ~/.terraformrc
    
    # define path to downloaded / cached plugins (default)
    plugin_cache_dir = "$HOME/.terraform.d/plugin-cache"
    
    provider_installation {
      # Use /home/developer/tmp/terraform-null as an overridden package directory
      # for the hashicorp/null provider. This disables the version and checksum
      # verifications for this provider and forces Terraform to look for the
      # null provider plugin in the given directory.
      filesystem_mirror {
        path    = "$HOME/.terraform.d/plugins"
        include = ["hashicorp/aws","hashicorp/*"]
      }

      #dev_overrides {
      #  "hashicorp/aws" = "$HOME/go/bin/terraform-provider-aws"
      #}

      # NOTE: If you omit `direct{}` block, Terraform will _only_ search the dev_overrides block
      #       and so no other providers will be available.
      # For all other providers, install them directly from their origin provider
      # registries as normal.
      direct {
        exclude = ["registry.terraform.io/hashicorp/aws"]
      }
    }
    ```


    
----
[^Terraform Docs: CLI Configuration File]: ((https://www.terraform.io/cli/config/config-file))
[^How to override a provider with a local version]: ((https://pet2cattle.com/2021/10/terraform-override-providers))
