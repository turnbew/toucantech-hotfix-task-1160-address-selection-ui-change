FOR PRIVACY AND CODE PROTECTING REASONS THIS IS A SIMPLIFIED VERSION OF CHANGES AND NEW FEATURES

TASK DATE: 19.10.2017 - FINISHED: 20.10.2017

TASK LEVEL: (EASY)  

TASK SHORT DESCRIPTION: 1160 ('address selection ui change' layout changing)

GITHUB REPOSITORY CODE: hotfix/task-1160-address-selection-ui-change

CHANGES

	IN FILES:
	
	admin_orders.php
		
		CHANGED CODE: 
			FROM: 
				if ! group_has_role('network_settings', 'content'))
				{
					redirect('admin-portal/error/no-permission/');
				}
			
			TO: 
				$method = $this->router->fetch_method();
				$exceptions = array("ajax_user_address");
				if (! in_array($method, $exceptions) and ! group_has_role('network_settings', 'content'))
				{
					redirect('admin-portal/error/no-permission/');
				}

	
	
	
	
	front_cart.php
	
		ADDED CODE 1: 
		
			->append_css('module::checkout.css')
			
		ADDED CODE 2: 
		
			...
			$data['is_user_logged'] = 0; 			//new one
			if( isset($this->current_user->id) ) { 	//already existed
				$data['is_user_logged'] = 1;		//new code
			....
			
			
			
			
			
	firesale.js
	
		ADDED BRAND NEW CODE: 
			
			
				/* ************************************************************* */
				/* ************************************************************* */
				/* ************************************************************* */
				/* 																							
				/*		CONTROL PART OF SHOP/CHEACKOUT/ADDRESS FIELDS
				/*
				/* ************************************************************* */
				/* ************************************************************* */
				/* ************************************************************* */

				
				
				//If one of the radio buttons changing in 'BILLING DETAILS' part
				$('.radio_bill_to').change(function() {
					clearInputFieldsData('.fields_container_bill_to');
					setLayoutChangesRadio('bill_to', $(this));
				})	
				
				
				
				
				
				//If 'My Billing and Shipping addresses are the same' checkbox changing
				$('#bill_details_same').change(function() {
					var checked = $(this).prop('checked');
					setLayoutChangesCheckbox('bill_to', checked);
					checked
						? copySelectedShippingAddress()
						: clearInputFieldsData('.fields_container_bill_to');
				})



				
				
				//If create new "bill" address radio button checked
				$('#bill_to_new').change(function() {
					var checked = $(this).prop('checked');
					setLayoutChangesCheckbox('bill_to', checked);
				})	
				

				
				

				//If one of the radio buttons changing in 'SHIPPING DETAILS' part
				$('.radio_ship_to').change(function() {
					clearInputFieldsData('.fields_container_ship_to');
					setLayoutChangesRadio('ship_to', $(this));
					copySelectedShippingAddress();
				})
				
				
				
					
					
				//If 'use my profile data address' checkbox changing
				$('#ship_to_profile_address').change(function() {
					var checked = $(this).prop('checked');
					setButtonsDisabledProperty('ship_to', checked);
					setLayoutChangesCheckbox('ship_to', checked);
					checked
						? setProfileAddressData()
						: clearInputFieldsData('.fields_container_ship_to');
				})
				
				
				
				
				//If create new "ship" address radio button checked
				$('#ship_to_new').change(function() {
					var checked = $(this).prop('checked');
					setLayoutChangesCheckbox('ship_to', checked);
					if (!$('#ship_to_profile_address').prop(checked)) clearInputFieldsData('.fields_container_ship_to');
				})	

				
				
			
				
				//Copy shipping details if input fields are changes
				$('.input_field_ship_to :input').on('change keyup', function() {
					if ($('#bill_details_same').prop('checked')) {
						$('#' + $(this).prop('id').replace('ship', 'bill')).val($(this).prop('value'));
					}
				});
				
				
				
				
				
				function setLayoutChangesCheckbox(selector, checked) {
					if ($('.radio_' + selector).length) {
						$('.radio_' + selector).prop('checked', false);
						$('.tr_' + selector).removeClass('active_' + selector).addClass('inactive');		
						.............
					}
				}	
				
				
				
				
				
				function setLayoutChangesRadio(selector, obj) {
					$('.radio_' + selector).prop('checked', false);
					$('.tr_' + selector).removeClass('active_' + selector).addClass('inactive');
					obj.prop('checked', true);
					.............
				}		
				
				
				
				
				
				function setButtonsDisabledProperty(selector, checked) {
					$('.radio_' + selector  + ', .checkbox_' + selector).prop('disabled', checked);
				}
				
				
				
				
				
				function setProfileAddressData() {
					$.get(SITE_URL + 'network_settings/content/shop/orders/ajax_user_address/' + USER_ID, function(response) {
						if ( response != 'false' ) {
							try {
								var data = $.parseJSON(response);  
								for( var k in data ) { 
									$('#ship_' + k).val(data[k]); 
								}
							}
							catch (e) {
								console.log('Response is not a JSON object');
							}
						}
						setButtonsDisabledProperty('ship_to', false);
						copySelectedShippingAddress();
					});	
				}
				
				
				
				
				
				function clearInputFieldsData(selector) {
					$(selector).find('input').val('');
				}
				
				
				
				
				
				function copySelectedShippingAddress() {
					clearInputFieldsData('.fields_container_bill_to');
					var field_slugs = [
						'company',
						'firstname',
						'lastname',
						'email',
						'phone',
						'address1',
						'address2',
						'city',
						'county',
						'postcode',
						'country'		
					];
					................
				}
				
	
				/* ************************************************************* */
				/* 																							
				/*		END of CONTROL PART OF SHOP/CHEACKOUT/ADDRESS FIELDS
				/*
				/* ************************************************************* */
				
				
	
				
				
		checkout.css
		
			added file and code
			
				table.checkout_addresses {
					margin-top: 10px;
					margin-right: 20px;
					width: 95%;
					margin-left: 0px !important;
				}
				table.checkout_addresses td {
					padding: 3px; 
				}
				table.checkout_addresses td.input {
					vertical-align: top;
					padding-top: 2px;
					width: 20px !important;
				}
				table.checkout_addresses td.input label{
					margin: 0px;
					padding: 0px;
				}
				table.checkout_addresses td.gap_small {
					min-height: 5px !important;
				}
				table.checkout_addresses td.gap_big {
					min-height: 30px !important;
				}
				table.checkout_addresses td.tag {
					padding-top: 5px !important;
				}
				td.address_ship_to,
				td.address_bill_to {
					padding-top: 6px;
					font-size: 14px;
					font-weight: bold;
					margin-top: 5px;
					border-bottom: solid 1px #ccc;
				}
				td.address_bill_to  {
					border-bottom: solid 1px #3998ff; 
				}
				.fields_container_ship_to,
				.fields_container_bill_to {
					display: none;
					width: 95%;
					margin: 0px;
					padding: 0px;		
				}
				tr.active_ship_to td.list,
				td.fields_ship_to div.fields_container_ship_to {
					background-color: #fafafa;
				}
				tr.active_bill_to td.list,
				td.fields_bill_to div.fields_container_bill_to {
					background-color: #f1f8ff;
				}
				tr.inactive td.list {
					background-color: #fff;
				}
				td.fields_ship_to input, 
				td.fields_ship_to select,
				td.fields_ship_to label,
				td.fields_bill_to input, 
				td.fields_bill_to select,
				td.fields_bill_to label {
					max-width: 80% !important;
					margin-left: 10%;
					margin-top: 4px;
					margin-bottom: 10px;
				}
				td.fields_ship_to label, 
				td.fields_bill_to label {
					margin-bottom: 0px;
				}
				
				
				
				
				
				
				
		checkout.php
		
			CHANGED CODE 
			
				TO 
				
				<div class="col-sm-4"><!-- START SHIPPING DETAILS -->
						<h5><?php echo lang('firesale:title:ship'); $display_ship_to = 'block;'; ?></h5>                       
							<fieldset>
								<table class="checkout_addresses">
						<?php if( $ship_req ): ?>
							<?php if( isset($addresses) && !empty($addresses) ): ?>
									<?php 
										$display_ship_to = 'none;';
										$counter = 1;
										foreach( $addresses AS $key => $address): 
									?>
									<tr>
										<td class="input">
											<label for="ship_to_<?php echo $address['id']; ?>">
												<input class="radio_ship_to address<?php echo $counter  ?>" name="ship_to" id="ship_to_<?php echo $address['id']; ?>" type="radio" value="<?php echo $address['id']; ?>"<?php echo ( ( isset($_POST['ship_to']) && $_POST['ship_to'] == $address['id'] ) || $key == 0 ? ' checked="checked"' : '' ); ?> />
											</label>	
										</td>
										<td class="address_ship_to">
											<?php echo $address['title']; ?>
										</td>
									</tr>
									<tr>
										<td colspan="2" class="gap_small"></td>
									</tr>
									................
									<tr><td class="gap_big" colspan="2">&nbsp;</td></tr>
									<?php
										$counter++;
										endforeach; 
									?>								
									<tr>
										<td class="input">
											<label for="ship_to_profile_address">
												<input class="checkbox_ship_to" name="ship_to_profile_address" id="ship_to_profile_address" type="checkbox"/>
											</label>										
										</td>
										<td class="tag">
											Use my profile address
										</td>
									</tr>
									<tr>
										<td class="input">
											<label for="ship_to_new">
												<input name="ship_to" id="ship_to_new" type="radio" value="new"<?php echo ( isset($_POST['ship_to']) && $_POST['ship_to'] == 'new' ? ' checked="checked"' : '' ); ?> />												
											</label>
										</td>
										<td class="tag">
											<?php echo lang('firesale:addresses:new_address'); ?>
										</td>
									</tr>
							<?php endif; ?>
									<tr>
										...............
									</tr>
								</table>
							</fieldset>							
						<?php endif; ?>
					</div><!-- END SHIPPING DETAILS -->
					
					
                    
                    <div class="col-sm-4"><!-- START BILLING DETAILS -->
                        <h5 class="bottom-buffer"><?php echo lang('firesale:title:bill'); $display_ship_to = 'block;';?></h5>						
						<fieldset>
							<table class="checkout_addresses">
							<?php if( isset($addresses) && !empty($addresses) ): ?>
								<?php 
										$display_ship_to = 'none;';
										$counter = 1;
										foreach( $addresses AS $key => $address): 
									?>
								<tr>
									<td class="input">
										<label for="bill_to_<?php echo $address['id']; ?>">	
											<input class="radio_bill_to address<?php echo $counter  ?>" name="bill_to" id="bill_to_<?php echo $address['id']; ?>" type="radio" value="<?php echo $address['id']; ?>"<?php echo ( ( isset($_POST['ship_to']) && $_POST['ship_to'] == $address['id'] ) || $key == 0 ? ' checked="checked"' : '' ); ?> />
										</label>	
									</td>
									<td class="address_bill_to">
										<?php echo $address['title']; ?>
									</td>
								</tr>
								<tr>
									<td colspan="2" class="gap_small"></td>
								</tr>
								<tr class="tr_bill_to address<? echo $counter . (($counter == 1) ? ' active_bill_to' : ' incative') ?>">
									<td class="empty"></td>
									..................
								</tr>
								<tr>
									<td class="gap_big" colspan="2">&nbsp;</td>
								</tr>
								<?php 
									$counter++;
									endforeach; 
								?>	
								<tr>
									<td class="input">
										<label for="bill_details_same">
											<input class="checkbox_bill_to" type="checkbox" name="bill_details_same" id="bill_details_same" value="yes" />
										</label>
									</td>
									<td class="tag">
										My Billing and Shipping addresses are the same.
									</td>
								</tr>
								<tr>
									<td class="input">
										<label for="bill_to_new">
											<input name="bill_to" id="bill_to_new" type="radio" value="new"<?php echo ( isset($_POST['bill_to']) && $_POST['bill_to'] == 'new' ? ' checked="checked"' : '' ); ?> />
										</label>
									</td>
									<td class="tag"><?php echo lang('firesale:addresses:new_address'); ?></td>
								</tr>
							<?php endif; ?>
								<tr>
									..................
								</tr>
							</table>
						</fieldset>
					</div><!-- END BILLING DETAILS -->
