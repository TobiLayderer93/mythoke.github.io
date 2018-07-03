---
title: "Use rspec in Jupyiter Notebook with Ruby Kernel"
date: 2018-07-03
tags: [Jupyter Notebook, Ruby, rspec]
excerpt: "Jupyter Notebook, Ruby, rspec"
---

# How to use rspec in Jupyter Notebooks with Ruby Kernel 


Here are some examples how to use rspec in Jupyter Notebooks with Ruby 2.3.3 Kernel: 




```ruby


transform_message = {
                      product: {
                        productMain: {
                          id: 1691469,
                          subsidiary:"BE",
                          name:"V8 ABSOLUTE NEW",
                          name2:"DYSON",
                          createTime:"2017-08-29T09:27:12.693+02:00",
                          updateTime:"2018-05-04T15:25:20.629+02:00",
                          rmsBlockingFlag:false,
                          deletedFlag:false,
                          toBeDeletedFlag:false,
                          manufacturerOrderNumber:"V8 ABSOLUTE NEW",
                          manufacturer:{
                            manufacturerId:15,
                            manufacturerUdaId:15
                            }
                          },
                         productSupplier:[
                            {
                              supplierID:3601252,
                              deletedFlag:false,
                              productEAN:[
                                {
                                ean:"5025155031666",
                                masterEanFlag:true,
                                deletedFlag:false
                                }
                              ]
                            }
                          ]
                        }
                      }

class TransformMessage 

  def self.transformation(input)
        transformed_hash = {}
        #transformed_hash.merge!(extract_alice(input))
        transformed_hash.merge!(extract_basics(input))
        transformed_hash.merge!(extract_eans_and_masterean(input))
        transformed_hash
      end
  
      private
  
      #def extract_alice(input)
       # input.select { |key, _| key.to_s.start_with?('alice_') }
      #end
  
      def self.extract_basics(input)
        {
          mdng_id: input.dig(:product, :productMain, :id),
          subsidiary: input.dig(:product, :productMain, :subsidiary),
          deleted: input.dig(:product, :productMain, :deletedFlag)
        }
      end
  
      def self.extract_eans_and_masterean(input)
        valid_eans = suppliers(input).map { |supplier| eans(supplier) }.flatten
  
        eans = valid_eans.map { |e| e[:ean].to_i }.uniq.sort
        master = master_ean(valid_eans)
  
        { ean_list: eans, master_ean: master }
      end
  
      def self.suppliers(input)
        supplier = input.dig(:product, :productSupplier)
        return [] if supplier.nil?

        supplier.select { |s| s.dig(:deletedFlag) == false }
        return supplier
      end
  
      def self.eans(supplier_hash)
        supplier_hash.dig(:productEAN).select do |ean|
          (ean.dig(:deletedFlag) == false) && !ean.dig(:ean).nil?
        end
      end
  
      def self.master_ean(eans)
        return nil if eans.empty?
        eans.find { |ean| ean.dig(:masterEanFlag) == true }.dig(:ean).to_i
      end
  
  
end 


```

This example transforms a message given as *transform_message* into a special format. 

Here are the rspec test for the given class: 

```ruby

transformation_class_spec = describe TransformMessage do
  
    let(:result) {TransformMessage.transformation(transform_message)}


    describe 'for product basics' do
      it 'extract the subsidiary' do
        expect(result[:subsidiary]).to eq 'BE'
      end
     
      it 'extract the id' do
        expect(result[:mdng_id]).to eq 1691469
      end
    end

    describe 'master_ean' do
      it 'returns the master_ean' do
        expect(result[:master_ean]).to eq 5025155031666
      end
      it 'does not return deleted Master ean' do
        expect(result[:master_ean]).not_to eq 2222222222222
      end
      context 'for deleted supplier' do
        it 'does not return the master ean' do
          expect(result[:master_ean]).not_to eq 1111111111111
        end
      end
    end
end

transformation_class_spec.run


```

If you want to have more information about rspec here is some [tutorial](https://www.tutorialspoint.com/rspec/index.htm)



