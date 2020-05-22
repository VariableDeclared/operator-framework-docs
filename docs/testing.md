# Testing

## Add test folder and test_requirements.txt

In the top level of your charm folder, create a new folder called test and add a file called test_requiments.txt.

Add the following to your test_requirements.txt, if this file does exist, create it under the test directory:

```
pyyaml
```

It is abvisable to create yourself a virtualenv to run these tests, you can read setting up a virtualenv [here](https://virtualenvwrapper.readthedocs.io/en/latest/).

## Creating tests

The operator framework comes with a [testing harness](https://github.com/canonical/operator/pull/146#issue-379532107).


```
class TestMyCharm(unittest.TestCase):
  def setUp(self):
    # possibly we just point the test at the charm's metadata.yaml directly
    self.charm, self.harness = setup_charm(MyCharm, '''
      name: mycharm
      provides:...
      requires:
        foo:
          interface: frob
      ''')

    def test_relation_changed(self):
        rel_id = self.harness.add_relation('foo', 'foo-provider')
        self.harness.add_relation_unit(rel_id, 'foo-provider/0', remote_unit_data={'foo': 'bar'})
        # inspect the initial state of the charm
        self.assertEqual(self.charm.state.foo, 'bar')
        self.harness.update_relation(rel_id, 'foo-provider/0', {'foo':'baz'})
        self.assertEqual(self.charm.state.foo, 'baz')
```

You will find a testing.py file in the Framework.

To test you charm you will need to import this harness along with your other charm imports into a new test file.

```
import sys
import unittest
... other imports e.g.
from src.charm import OSMUIK8sCharm

sys.path.append('lib')
from ops.testing import Harness

```

Now that you have the harness imported, you can go ahead and create our test class:

```
class TestYourCharmName(unittest.TestCase):

```


One of the methods of testing that the operator offers is to test the leader, we can do that by using the `set_leader` Operator Framework method:

```
    def test_set_leader(self, *args):
        harness = Harness(OSMUIK8sCharm)

        harness.set_leader(False)
        harness.begin()
        self.assertFalse(harness.charm.model.unit.is_leader())
```

Once you have added a test method, go ahead and run the tests, making sure your virtualenv is enabled you can run something like:

```
(osm-ui-charm) % python -m unittest test/test_charm.py
```

## Adding more test methods

