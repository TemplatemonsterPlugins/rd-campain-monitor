# Сampain monitor

You need to take the sources from the repository file js/core.min.js

To initialize the form, you need to insert this JS code.

	  /**
	   * Global variables
	   */

	  var $document = $(document),
	    plugins = {
	      campaignMonitor: $('.campaign-mailform'),
	      regula: $("[data-constraints]"),  
	      rdInputLabel: $(".form-label"),
	    };

	  /**
	   * Initialize All Scripts
	   */
	  $document.ready(function () {
	    var isNoviBuilder = window.xMode;


	    /**
	     * attachFormValidator
	     * @description  attach form validation to elements
	     */
	    function attachFormValidator(elements) {
	      for (var i = 0; i < elements.length; i++) {
	        var o = $(elements[i]), v;
	        o.addClass("form-control-has-validation").after("<span class='form-validation'></span>");
	        v = o.parent().find(".form-validation");
	        if (v.is(":last-child")) {
	          o.addClass("form-control-last-child");
	        }
	      }

	      elements
	        .on('input change propertychange blur', function (e) {
	          var $this = $(this), results;

	          if (e.type !== "blur") {
	            if (!$this.parent().hasClass("has-error")) {
	              return;
	            }
	          }

	          if ($this.parents('.rd-mailform').hasClass('success')) {
	            return;
	          }

	          if ((results = $this.regula('validate')).length) {
	            for (i = 0; i < results.length; i++) {
	              $this.siblings(".form-validation").text(results[i].message).parent().addClass("has-error")
	            }
	          } else {
	            $this.siblings(".form-validation").text("").parent().removeClass("has-error")
	          }
	        })
	        .regula('bind');

	      var regularConstraintsMessages = [
	        {
	          type: regula.Constraint.Required,
	          newMessage: "The text field is required."
	        },
	        {
	          type: regula.Constraint.Email,
	          newMessage: "The email is not a valid email."
	        },
	        {
	          type: regula.Constraint.Numeric,
	          newMessage: "Only numbers are required"
	        },
	        {
	          type: regula.Constraint.Selected,
	          newMessage: "Please choose an option."
	        }
	      ];


	      for (var i = 0; i < regularConstraintsMessages.length; i++) {
	        var regularConstraint = regularConstraintsMessages[i];

	        regula.override({
	          constraintType: regularConstraint.type,
	          defaultMessage: regularConstraint.newMessage
	        });
	      }
	    }


	    /**
	     * isValidated
	     * @description  check if all elements pass validation
	     */
	    function isValidated(elements, captcha) {
	      var results, errors = 0;

	      if (elements.length) {
	        for (j = 0; j < elements.length; j++) {

	          var $input = $(elements[j]);
	          if ((results = $input.regula('validate')).length) {
	            for (k = 0; k < results.length; k++) {
	              errors++;
	              $input.siblings(".form-validation").text(results[k].message).parent().addClass("has-error");
	            }
	          } else {
	            $input.siblings(".form-validation").text("").parent().removeClass("has-error")
	          }
	        }

	        if (captcha) {
	          if (captcha.length) {
	            return validateReCaptcha(captcha) && errors === 0
	          }
	        }

	        return errors === 0;
	      }
	      return true;
	    }


	    /**
	     * RD Input Label
	     * @description Enables RD Input Label Plugin
	     */

	    if (plugins.rdInputLabel.length) {
	      plugins.rdInputLabel.RDInputLabel();
	    }


	    /**
	     * Regula
	     * @description Enables Regula plugin
	     */

	    if (plugins.regula.length) {
	      attachFormValidator(plugins.regula);
	    }


	    /**
	     * Campaign Monitor ajax subscription
	     */
	    if (plugins.campaignMonitor.length) {
	      for (var i = 0; i < plugins.campaignMonitor.length; i++) {
	        var $campaignItem = $(plugins.campaignMonitor[i]);

	        $campaignItem.on('submit', $.proxy(function (e){
	          var data = {},
	            url = this.attr('action'),
	            dataArray = this.serializeArray(),
	            $output = $("#" + plugins.campaignMonitor.attr("data-form-output")),
	            $this = $(this);

	          for (i = 0; i < dataArray.length; i++) {
	            data[dataArray[i].name] = dataArray[i].value;
	          }

	          $.ajax({
	            data: data,
	            url: url,
	            dataType: 'jsonp',
	            error: function (resp, text) {
	              $output.html('Server error: ' + text);

	              setTimeout(function () {
	                $output.removeClass("active");
	              }, 4000);
	            },
	            success: function (resp) {
	              $output.html(resp.Message).addClass('active');

	              setTimeout(function () {
	                $output.removeClass("active");
	              }, 6000);
	            },
	            beforeSend: function(data){
	              // Stop request if builder or inputs are invalide
	              if (isNoviBuilder || !isValidated($this.find('[data-constraints]')))
	                return false;

	              $output.html('Submitting...').addClass('active');
	            }
	          });

	          return false;
	        }, $campaignItem));
	      }
	    }
	  });


## Campaign Monitor

To connect the subscription form to your Campaign Monitor account, you need to go to the Lists page (1), create a list (2) or select an existing one (3).

![](https://www.templatemonster.com/help/quick-start-guide/website-templates/v1-4/img/campain.png)

Go to the “Sign up forms” section.

![](https://www.templatemonster.com/help/quick-start-guide/website-templates/v1-4/img/campain-1.jpg)

Then select the “Copy/paste a form to your site” section.

![](https://www.templatemonster.com/help/quick-start-guide/website-templates/v1-4/img/campain-2.png)

Afterward, generate the form code.

![](https://www.templatemonster.com/help/quick-start-guide/website-templates/v1-4/img/campain-3.png)

Finally, copy the action attribute of form element and attribute name of input[type='email'] element of the form you generated and paste it into the form of your website. 
Here’s an example of the form code:

    <!-- Campaign Monitor-->
    <form class="campaign-mailform form-inline" data-form-output="form-output-global" action="https://templatermonster.createsend.com/t/j/s/zjjikd/" method="post">
      <div class="form-wrap">
        <label class="form-label" for="campaign-email">Enter your e-mail</label>
        <input class="form-input" id="campaign-email" type="email" name="cm-zjjikd-zjjikd" data-constraints="@Email @Required">
      </div>
      <div class="form-button">
        <button class="button button-primary" type="submit">Subscribe</button>
      </div>
    </form>

    <!-- Element for for resault -->
    <div class="form-output-global"></div>